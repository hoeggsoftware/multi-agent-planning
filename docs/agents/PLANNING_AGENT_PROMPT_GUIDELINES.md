# Planning Agent - Prompt Generation Guidelines

Sections referencing canonical principles in this document point at the principle files at `docs/principles/`. The multi-agent planning model in [`docs/model.md`](../model.md) describes how operator umbrellas and tenant projects inherit these principles via the bimodal inheritance model.

## Prompt File Organization

When generating prompts for engineer agents or other specialized agents, follow these organization guidelines:

### Location Structure

**Active Prompts**: `docs/agents/prompts/active/{category}/`

Categories:
- `infrastructure/` - Infrastructure components, daemon configuration, deployment
- `feature/` - New capabilities and enhancements
- `bugfix/` - Bug fixes and corrections
- `documentation/` - Documentation tasks, updates, cleanup

**Archives**: `docs/agents/prompts/archived/{YYYY-MM}/`

Example: `archived/2026-05/` for May 2026 completed tasks

### Naming Convention

Use descriptive, dated filenames for task-specific prompts:
```
{category}_{description}_{YYYYMMDD}.md
```

Examples:
- `infrastructure_umbrella_bootstrap_20260504.md`
- `feature_github_mcp_server_20260510.md`
- `bugfix_daemon_retry_logic_20260515.md`
- `documentation_component_claude_md_20260520.md`

### Prompt Structure

Every prompt should include:

1. **Header Section**:
   ```markdown
   # [Task Title]

   **Date**: [YYYY-MM-DD]
   **Agent Type**: [Planning/Engineer/Specialist]
   **Estimated Time**: [X hours]
   **Priority**: [Critical/High/Medium/Low]
   **Category**: [infrastructure/feature/bugfix/documentation]
   ```

2. **Preconditions** - Expected repository state at dispatch time. See "State Preconditions for Engineer Prompts" below for full requirements and format. This block belongs immediately after the Header and before Objective; the engineer's first action is verifying it.

3. **Objective** - Clear, concise goal

4. **Background** - Context and current state

5. **Context to Read Before Starting** - Specific list of docs the engineer must consult before beginning work. This is REQUIRED, not optional. Anticipate which docs are relevant to prevent the engineer from rediscovering documented patterns through trial-and-error.

   Example structure:
   ```markdown
   ## Context to read before starting

   In this order:

   1. `CLAUDE.md` — umbrella overview and working environment
   2. `plan-sync/CLAUDE.md` — component-specific architecture and deployment
   3. `implementation_logs/{previous_work}.md` — what was done previously
   ```

6. **Implementation Steps** - Detailed task breakdown

7. **Success Criteria** - How to verify completion

8. **Rollback Plan** - How to undo if needed (for infrastructure changes)

9. **Documentation Updates** - Which files to update after completion

10. **Post-Task Cleanup**:
   ```markdown
   ## Post-Task Cleanup

   After completing this task:
   1. Update CLAUDE.md with status changes (if applicable)
   2. Create implementation log in `/implementation_logs/`
   3. Move this prompt to `/docs/agents/prompts/archived/{YYYY-MM}/`
   4. Update any cross-referenced documentation
   ```

#### For prompts touching production state

Infrastructure-category prompts — plus any feature or bugfix prompt that modifies live daemons, deployed services, or production configuration — must include deployment discipline criteria in their success criteria, so the engineer's deliverable produces verification evidence for each. This mirrors the Documentation Quality Standards pattern below: documentation prompts require read-back verification; production-touching prompts require deployment verification.

Five criteria, each derived from a specific deployment failure mode:

- **Stage, don't edit live.** The engineer must work in a separate file from the production artifact. The prompt's success criteria require the staged file to exist, to be the file tested, and to be the file copied into place at deploy time. Live production code is never the working surface during development.

- **Tests pass with hard evidence before any deploy step.** Required regression tests must complete with their output captured (not just observed) and pass/fail status recorded before any deploy step runs. The criterion is hard evidence on disk that an external reviewer could verify, not "the engineer reports tests passed."

- **Backup and verify the backup before any overwrite.** Before any operation that overwrites a production file, the engineer must take a backup and then verify the backup is intact and matches the source before proceeding to the overwrite step. The verification gate prevents mid-procedure mistakes from destroying the work being deployed.

- **Verify the deployed code is actually running, not just present on disk.** "The file changed" is not "the code is running" anywhere there's process state involved. On launchd-managed daemons specifically, the previous version stays in memory across script-file changes; explicit reload is required, and the prompt's success criteria must require behavioral evidence that the new code path executed in the running process — not just that the file on disk was updated.

- **Inspect persistent state for stale residue from prior runs.** Stateful components — circuit breaker state, caches, lock files, queues — carry forward across reloads and across deploys. Before declaring a deploy healthy, the prompt should require inspection of any state directories the deployed component owns, with a per-item decision: legitimate, or test residue from prior runs that should be cleared.

The shape of all five is the same: success criteria specify *what evidence the engineer must capture and report*, not procedural steps the engineer must perform. The engineer determines how to satisfy each criterion; the prompt specifies what satisfaction looks like.

#### For prompts referencing cross-repo content

When the prompt references content in a different repository than the engineer's working tree — typically umbrella canonical files referenced from a tenant repo, or peer-tenant references — name the cross-repo content with full repository qualification (e.g., `rhoegg/agentic-planning/docs/principles/<name>.md`) and let the engineer choose the tool used to read it. Do not prescribe GitHub MCP, `gh` CLI, browser fetch, or any other specific tool.

The WHAT is the path. The HOW is the engineer's choice based on what works in their environment.

This matters because cross-repo authorization differs by agent surface: an engineer's GitHub MCP installation may or may not have access to the target repo, while their `gh` CLI typically does. Prompts that prescribe a specific tool can produce unnecessary halts when access patterns differ from the prompt-author's expectation; prompts that name the path neutrally permit the engineer to substitute the tool that works in their environment.

See [`docs/operator-onboarding.md`](../operator-onboarding.md) Step 5 for the multi-app authorization model that motivates this discipline.

## State Preconditions for Engineer Prompts

When an engineer prompt references specific repository state (commits, file content, or paths), include a precondition block as the engineer's first verification step. The precondition establishes the expected git state at dispatch time, allowing the engineer to confirm their local checkout matches the planning agent's view before proceeding. Without this gate, engineers may misread "the commits this prompt references do not exist" when their checkout is behind, or proceed against stale assumptions when the planning agent's view was stale.

The canonical principle is at [`docs/principles/state_preconditions.md`](../principles/state_preconditions.md). That file specifies what to include in the precondition block (base commit SHA, dispatch commit SHA, referenced commits, identifying file-content fragments), the format for the block, and the halt condition for engineers when preconditions don't match after fetch.

For prompts touching repository structure, the precondition block belongs immediately after the title, before Objective.

## Local Working Tree Check

State Preconditions verifies expected git state at session start. Local Working Tree Check is its sibling: verify the local working tree's state, including uncommitted and untracked files, before acting.

### Why this exists

State Preconditions catches "your view of `main` is stale relative to the planning agent's view." Local Working Tree Check catches a different failure mode: "another agent on the same machine wrote a file that hasn't been committed yet, and you assumed it didn't exist because it isn't in remote `main`."

Engineer agents running on local CLI surfaces (Claude Code, Codex, opencode, Aider, others) write to the working tree by default. Committing and pushing are separate explicit actions. When a planning-agent prompt references an artifact ("see the review log at `implementation_logs/foo.md`"), the engineer's first check is: does this file exist on disk? Not: is this file in `git log` or `origin/main`?

When the answer is "exists on disk but not in remote," the working tree is the authoritative source of recent work until commit-and-push lands.

### Engineer-side discipline

At session start:

- Treat `git status` output as part of the orienting context. Untracked files, modified files, and unstaged changes are all real artifacts that may affect the work.
- For each artifact the prompt names by path, verify existence on disk (via `ls` or `cat`), not via `git log` or remote-state queries.
- If a referenced artifact is not on disk and not reachable from remote `main`, surface to the planning agent — do not substitute a different source. The artifact may need to be re-generated, re-committed, or the prompt may be referring to something that doesn't exist yet.
- Do not assume "not committed" means "doesn't exist." On a multi-agent local clone, the working tree is the authoritative source of recent work until commit/push lands.

### Planning-agent-side discipline

Engineer prompts whose task may read, move, delete, or archive repo files must include an explicit Local Working Tree Check directive in the preconditions or implementation steps. Documentation-category prompts qualify by default; infrastructure or feature prompts qualify when they touch repo files (not just deployed services). The directive names the working-tree check explicitly so the engineer is not relying on session-start habit alone.

### Composition with Hard Evidence

Local Working Tree Check is an application of the Hard Evidence Principle to local file state: verify by direct observation (the working tree) rather than by inference from remote state. The pairing is intentional — the same discipline that says "verify before concluding" against system state says "verify before assuming absent" against local files.

This discipline is distinct from verifying whether your own write landed after an interrupted response, and from committing early to protect your own long doc-only work; this check is about discovering other agents' local artifacts before assuming they are absent.

## Maintaining the WHAT/WHY boundary

The Conventions section of an operator umbrella's project instructions establishes the WHAT/WHY rule: prompts define what should change and why; engineers determine how. This is the load-bearing convention that keeps the multi-agent system multi-agent — engineers reduced to typists is the failure mode this convention exists to prevent.

The rule has been violated in past incidents. The violations share patterns. Naming the patterns helps future agents recognize the temptation in real time, before content with the wrong shape ships.

### Three conditions where the discipline is hardest

**Mode-switch from troubleshooting to dispatch.** The convention's exception clause permits brief command examples in real-time chat with an operator when troubleshooting an issue together. When that chat content is then asked to be reshaped into an engineer prompt, the temptation is to relabel the audience without reshaping the content. The chat was commands because the operator was running them on their own machine; the prompt for an engineer requires WHAT and WHY. Reshaping audience without reshaping content collapses planning into runbook-writing.

Defense: when content moves from chat-to-operator to any other audience, treat reshape as the work, not a step you can skip.

**Source material already in command form.** When the recovery procedure, README, or onboarding doc being translated from is itself written as commands — appropriately, since it's engineer-facing — the path of least resistance is to condense and sequence those commands. Translation to WHAT and WHY requires active rewriting effort that isn't natural under time pressure.

Defense: if a prompt's deliverables or steps are recognizable commands lifted from source material, the translation hasn't happened yet.

**Time pressure.** Under operational urgency — incident in flight, a colleague waiting, a five-minute window — the pull is toward "preserve content, change framing." Time pressure is not an exception to the convention; it is the condition that most reliably erodes it.

Defenses: extend the operational window with the operator if it's safe; stay in chat-with-operator mode (where commands are fine) rather than escalating to a prompt; or send the engineer at source material with the situation framed and let them choose the steps.

### The mid-draft self-test

An operator umbrella's project instructions' self-test catches all three conditions if applied honestly: *is the engineer making implementation decisions, or merely typing what you wrote? If the answer is typing, the prompt is over-specified. Strip back to WHAT and WHY before sending.*

The test is brief enough to apply mid-draft. The temptation under pressure is to skip it. The discipline is treating the test as a hard step before any prompt leaves your hands.

## Disclosure Is Not Correction

When about to commit content known to be incomplete or incorrect, honest disclosure in the commit message does not redeem the commit. The destructive effect lands on `main` the moment the commit is pushed. This rule is load-bearing because coordination state IS git state — intentionally landing wrong content under cover of an apology breaks the assumption that other agents rely on when reading the repo as authoritative.

The canonical principle is at [`docs/principles/disclosure_is_not_correction.md`](../principles/disclosure_is_not_correction.md). That file describes the failure pattern (agent perceives constraint, ships degraded version with disclosure, damage propagates), the rule (test whether the constraint is real; hand off to a capable surface; or write a cleanup prompt and stop — never commit degraded content), and the test-before-defer discipline for assumed tool limitations.

Planning agents writing engineer prompts should ensure the dispatch makes the rule explicit when the engineer's work involves rewriting files that have an extensive prior chronicle.

## Cleanup Responsibility

Prompts should be archived when:
- ✅ Task is completed
- ✅ Prompt is older than 1 week and not actively referenced
- ✅ Task has been superseded by a new approach
- ✅ Implementation log has been created documenting the work

Prompts should remain active when:
- ⏸️ Task is in progress
- ⏸️ Task is blocked but will resume soon
- ⏸️ Task is part of ongoing work

## Documentation Quality Standards

This section covers verification requirements specific to documentation prompts. Production-touching prompts have a parallel category-keyed pattern; see "For prompts touching production state" under Prompt Structure above.

**CRITICAL**: Work is not complete until verified.

### Verification Is Mandatory

Before reporting any documentation work complete, agents must:

1. **Read back the file** to verify specific content added is actually present
2. **Check cross-references** resolve correctly
3. **Include verification evidence** in completion report

### What "Complete" Really Means

```
Claiming work is complete ≠ Work is actually complete

Complete means:
✅ Changes exist in the files
✅ Cross-references work
✅ Content is discoverable
✅ You verified by reading it back
✅ You included evidence in your report

NOT complete:
❌ You wrote content but didn't save
❌ You think you updated it but didn't check
❌ Changes are partial or incomplete
❌ You report "done" without verification
```

## Prompt Lifecycle

The commit is the dispatch. A prompt is durably available to engineers the moment it lands on `main`; no separate sync step is required.

A chat "yes" — including one that approves dispatch — is not a commit. To keep "approval" and "dispatch" from collapsing into one, the planning agent's discipline at handoff is: commit, read back from `main` via the same surface a peer would use, and report the commit SHA. Only then is the dispatch complete and the engineer can be pointed at the path.

```
CREATE NEW PROMPT:
1. Determine category (infrastructure/feature/bugfix/documentation)
2. Generate prompt with proper naming: {category}_{description}_{YYYYMMDD}.md
3. Draft prompt body, including cleanup instructions and cross-references to related documentation and prompts
4. Commit prompt to docs/agents/prompts/active/{category}/ on main and push
5. Read back from main, confirm the commit SHA, and report it — the dispatch is complete only when the artifact is verifiably on main

PROMPT LIFECYCLE:
Active → Complete → Archive
/active/{category}/ → Engineer executes → /archived/{YYYY-MM}/
```

The full coordination pattern is described in [`docs/model.md`](../model.md) and [`docs/operator-onboarding.md`](../operator-onboarding.md).

## Hard Evidence Principle

All decisions, conclusions, and reports must be based on hard evidence gathered from direct observation, not on assumptions or memory. This applies to both planning agents and engineer agents — verify before concluding rather than reasoning from "this is probably the case." Planning agents in particular must verify engineer reports against direct observation of the artifacts they describe; "the engineer says the file exists" is not evidence.

The canonical principle is at [`docs/principles/hard_evidence.md`](../principles/hard_evidence.md). That file specifies the principle's specific applications (diagnostic conclusions, system state claims, component interactions, agent reports, surprising findings), the cost discipline, and the discipline for when verification isn't possible.

## Documentation-First Discipline

When you hit an obstacle whose shape suggests "infrastructure or process I expected to work doesn't," your first move is to consult project documentation before iterating with variations. Multi-agent projects invest effort capturing operational knowledge in architecture docs, procedure docs, and lessons-learned files specifically so future agents don't re-discover the same obstacles.

The canonical principle is at [`docs/principles/documentation_first.md`](../principles/documentation_first.md). That file specifies when this applies (access failures, configuration surprises, status mismatches, behavior surprises, tool failures, unrecognized errors), the discipline of pausing to consult docs before iterating, and the composition with Hard Evidence.

Planning agents writing engineer prompts should anticipate which documentation will be relevant and list it explicitly in the prompt's Context to Read section. This is the planning-agent application of the discipline — pre-identify the relevant docs at prompt-authoring time, so engineers don't need to rediscover them in real time.

## Governance Boundary

Planning agents may revise both:
- These planning agent guidelines (`PLANNING_AGENT_PROMPT_GUIDELINES.md`)
- Engineer-facing umbrella docs (umbrella `CLAUDE.md`, component docs)

Engineer agents work within the guidelines but do not edit planning agent guidelines. If an engineer discovers a gap or issue in these guidelines, they should note it in their completion report for the planning agent to address.

---

**Last updated:** 2026-06-06

History is preserved in the git log of this repository.
