# Tenant onboarding

This template guides setting up a project as a tenant of an existing operator umbrella under the multi-agent planning model. If you haven't read [`docs/model.md`](./model.md) and your operator hasn't set up their operator umbrella per [`docs/operator-onboarding.md`](./operator-onboarding.md), start there. This guide assumes the layered architecture (public canonical, operator umbrella, tenant project) is in place upstream.

This template is intentionally generic. Your operator umbrella may have its own tenant onboarding checklist that elaborates these phases with operator-specific conventions (file paths, prompt categories, governance specifics). When the operator has their own checklist, follow it; this template is the model the operator's checklist is derived from.

## Preconditions

Before starting, confirm:

- **The new project participates in cross-tenant inheritance.** Projects that consume documentation read-only — referencing principles without participating in the inheritance model's structural commitments — are not tenants in this sense. They just read the published canonical content.
- **The tenant repo exists** with a defined organization and path, or you have permission to create it.
- **Your operator umbrella exists** with `INHERITED_PRINCIPLES.md` and `CANONICAL_PRINCIPLES.md` in place.
- **You have read and write access** to the tenant repo, and read access to the operator umbrella. If GitHub MCP returns 404 on cross-repo reads, see your operator's cross-repo access setup (Step 5 of operator onboarding) for the fallback paths.
- **Your operator's `CANONICAL_PRINCIPLES.md` is current** and reflects what your operator exposes to tenants. The principles listed there are what you can inherit; the Origin column tells you where each principle's canonical source currently lives.

## Greenfield vs. mature-project adoption

This template accommodates two distinct shapes of tenant onboarding:

**Greenfield**: a new project that doesn't yet exist as a repo. You're creating the repo, the planning artifacts, and the inheritance setup together. Phase A's bootstrap is fully applicable; the project structure is yours to design within the recommended layout.

**Mature-project adoption**: an existing project with substantial governance (existing CLAUDE.md, planning artifacts, engineer prompts) that you're bringing into the inheritance model. Phase A is largely already complete — the structure exists. Substantive work concentrates in Phase B (composition check), Phase D (adapted-mode entries), and Phase E (documenting tenant-local principles that pre-existed the inheritance model).

The phases below apply to both. Where they differ, this is named explicitly. The shape difference matters most for Phase A and for the metadata-staleness pitfall in the Common Pitfalls section.

## Phase A: Bootstrap tenant repo structure

For greenfield tenants, create the standard planning-agent directory structure:

- `docs/agents/` — planning-agent governance docs
- `docs/agents/INHERITED_PRINCIPLES.md` — initially empty (header + table skeleton); populated in Phase C/D
- `docs/agents/PLANNING_AGENT_PROMPT_GUIDELINES.md` (optional) — tenant-specific PAG. Your operator may have one you can reference, or you may need only tenant-local conventions.
- `docs/agents/prompts/active/` and `docs/agents/prompts/archived/` — active and archived prompt directories with category subdirectories (your operator may have category conventions to follow)
- `implementation_logs/` — for engineer and planning-agent work logs
- `CLAUDE.md` at the repo root — orientation for both planning agents and engineers

For mature-project adoption, the existing structure typically already includes these or analogous directories. The work is recognizing what's in place rather than creating it. Your CLAUDE.md may have a different shape than the recommended one — that's fine; tenant-local convention is supported by the model.

For tenants with substantial multi-repo or multi-surface complexity, also create:

- `docs/architecture/` — design docs that aren't principles
- Tenant-specific procedure docs as needed

## Phase B: Inheritance decisions

For each principle in your operator's `CANONICAL_PRINCIPLES.md`, decide reference mode vs. adapted mode. Do this in one pass for all available principles, not iteratively — the cross-principle composition often informs decisions.

For each principle:

**Read the canonical text** at the Canonical source path declared in your operator's registry. The path resolves per Origin:

- `Origin=upstream`: the canonical lives at a public canonical path (e.g., this repository's `docs/principles/<name>.md`).
- `Origin=adapted-upstream`: the canonical lives in your operator umbrella's `docs/principles/`. Read both the operator's adapted version and (if you want full context) the public canonical it derives from.
- `Origin=self`: the canonical lives in your operator umbrella's `docs/principles/`.

Read the full file; the canonical text is the substance, the scope note orients you.

**Composition check.** Examine your existing CLAUDE.md, PAG drafts (if any), architecture docs, and other governance content for:

- Sections with the same name or topic as the principle (suggests an existing tenant-local version that needs reconciling)
- Content that applies the principle in tenant-specific ways (operational caveats, infrastructure-specific applications, multi-repo or multi-surface handling)
- Restructuring constraints the tenant idiom imposes (e.g., engineer-facing top-level placement, fold-into-workflow patterns, audience-specific framing)

If any of these exist, the principle is a candidate for adapted mode. The tenant-specific content IS the adaptation rationale.

For mature-project adoption, the composition check is especially important. Existing tenant content that applies a principle in tenant-specific ways is adapted-mode inheritance, not tenant-canonical content. Treating it as tenant-canonical (and inheriting in reference mode) produces an inconsistent state: your INHERITED_PRINCIPLES.md says reference, your repo has a full local section with elaboration.

**Selection criterion**: choose adapted mode when restructuring or tenant-specific elaboration is genuinely needed. Choose reference mode when your operator's canonical (which may itself be public, adapted-upstream, or operator-originated) works for the tenant unchanged. Default to reference mode — it has zero ongoing maintenance cost. Adapted mode requires content re-translation on every canonical update; choose it only when the value of local restructuring or elaboration justifies that cost.

**Don't default to reference mode without doing the composition check.** The shortcut "we don't have a structural reason for adapted mode" is true on first inspection of many tenants, but tenant-specific elaboration often hides in existing PAG sections or CLAUDE.md content that wasn't framed as inheritance-related.

Record the mode decisions for all principles before proceeding to Phase C/D.

## Phase C: Adopt reference-mode principles

For each principle in reference mode:

- Add a row to `INHERITED_PRINCIPLES.md` with: name, mode `reference`, source path (the Canonical source declared in your operator's registry), source SHA at last verification, last verified date.
- Ensure the tenant's reading-order guidance (typically in CLAUDE.md) lists the canonical source path so planning agents read current canonical at session start.

Reference-mode entries require no local copy of the canonical text. The reading-order points at the operator's declared canonical source; planning agents read it fresh each session.

The recorded source SHA is a **last-verified marker, not a current pin**. Drift between recorded SHA and current canonical is normal for reference mode and is reconciled at periodic audit moments (re-runs of this checklist, first-session verifications, or operator-driven Origin or path changes). The whole value of reference mode is per-update zero work; treating Source SHA as a current pin would defeat that.

## Phase D: Adopt adapted-mode principles

For each principle in adapted mode:

- Determine the local location (typically a section in tenant PAG, a standalone discipline file, or a workflow doc — wherever the tenant's idiom places this kind of content).
- Author the local version. **The local content must reflect the canonical content at the named source SHA, translated to fit the tenant's placement.** If the canonical specifies a structure, the local version must include that structure. Restructuring is the placement work; the substantive content is the canonical text.
- Add a provenance marker at the top of the local version, immediately after the title: `*Adapted from <canonical-path>@<SHA>. See INHERITED_PRINCIPLES.md.*`
- Add a row to `INHERITED_PRINCIPLES.md` with: name, mode `adapted`, source path, source SHA at last sync, local location, adaptation rationale (one-line explanation of why the local form differs structurally).

The provenance marker's SHA records the canonical content the local version is synced to. When the canonical SHA bumps, a port re-translates the new content into the local placement and updates this SHA. **Bumping the marker SHA without re-translating content breaks the audit trail.** See Phase F.

For mature-project adoption: an existing tenant section that the composition check identified is the adapted-mode local placement. The content may already be present; the work is adding the provenance marker, recording the inheritance, and (if needed) reconciling the content against the current canonical state. Don't blindly overwrite existing content; verify it reflects current canonical first.

## Phase E: Document tenant-local principles

Some tenants have principles that are canonical to the tenant rather than inherited from the operator umbrella. They apply to the tenant's specific operational context and aren't part of cross-tenant inheritance.

For these:

- Document them in tenant PAG or CLAUDE.md with tenant-canonical framing (no provenance marker pointing at any canonical source above the tenant).
- Do NOT add them to `INHERITED_PRINCIPLES.md` — that file is for inherited principles only.
- If a tenant-local principle is a candidate for future promotion to operator umbrella (because it might apply to other tenants too), note that explicitly in the principle's documentation as a future consideration.

For mature-project adoption, tenant-local principles often pre-exist the onboarding. Phase E recognizes and formalizes what's already there, not necessarily authoring new content.

## Phase F: Update-protocol setup

For tenants with adapted-mode entries, ensure planning agents understand the update protocol:

- When the canonical SHA bumps for an adapted-mode principle, a port is required.
- The port involves reading the new canonical, re-translating its content into the local placement (preserving the placement, updating the content), then bumping the source SHA in `INHERITED_PRINCIPLES.md`.
- The translation IS the sync. SHA bumps without content translation produce an overclaim — the marker says the local version reflects canonical content at SHA X, when in fact it reflects an older content state.
- Reference-mode entries require no port action when the canonical SHA bumps. Their session-start reads pick up the new content automatically; their recorded source SHA stays at last-verified.

If your operator changes a principle's Origin (e.g., `upstream` → `adapted-upstream` because the operator is now restructuring it locally), that's a different kind of update — your operator should dispatch a tenant migration update to you. See your operator's docs or coordinate with the operator maintainer.

## Phase G: First-session verification

Before declaring the tenant onboarded, verify the setup with a planning-agent session:

- The planning agent reads tenant CLAUDE.md and follows reading-order references.
- For each inherited principle, the planning agent confirms the source path resolves (canonical is readable).
- For each adapted-mode entry, the planning agent reads both the local version and the canonical source and confirms the local version reflects the current canonical content per the recorded SHA.
- For tenant-local principles, the planning agent confirms they are documented and not erroneously included in `INHERITED_PRINCIPLES.md`.

If cross-repo canonical reads fail (404 or access denied), see your operator's cross-repo access setup for the multi-app authorization model and the `gh` CLI fallback.

This phase often surfaces drift that isn't a setup defect — most commonly, tracking metadata in CLAUDE.md (completed/active task lists, current phase markers, "Last updated" dates) that has gone stale since the last update. For mature-project adoption especially, the CLAUDE.md header often lags actual project state by weeks or months. Phase G surfacing this drift is correct discipline; reconciling it is separate work from the onboarding itself.

## Phase H: Register tenant in operator umbrella

Once the tenant is set up and verified, update the operator umbrella's registry to record the tenant's inheritance:

- Edit your operator's `CANONICAL_PRINCIPLES.md`.
- For each principle the tenant inherits, add the tenant's mode to the Tenant inheritance column.
- Add a changelog entry recording the tenant's addition and its initial mode set.

This is operator-maintainer work on the operator umbrella (not the tenant). It closes the onboarding loop — the operator's registry is now the authoritative record of who inherits what in what mode.

## Common pitfalls

Patterns from accumulated experience worth naming explicitly so future onboardings avoid them:

**Bumping SHA without translating content.** When the canonical evolves, the local adapted version's content must be re-translated. The marker SHA must match the content state, not just the most recent registered SHA. Bumping without translating is silent overclaim — the marker says the local version reflects content at SHA X when it actually reflects an older state. Cheap to verify by direct read-back; expensive to discover much later.

**Treating tenant-specific elaboration as tenant-canonical.** Existing tenant content that applies a canonical principle in tenant-specific ways is adapted-mode inheritance, not tenant-canonical content. The composition check in Phase B catches this. Skipping the check and defaulting to reference mode produces an inconsistent state: the inheritance index says reference, the tenant repo has a full local section with elaboration that should have been recognized as adapted-mode inheritance.

**Treating "repo artifacts in place" as "tenant onboarded."** The structural onboarding (Phases A–H) produces commits in the tenant repo and updates the operator's registry. But until the tenant's planning-agent session instructions are updated to reflect the new inheritance model — typically the project instructions in whatever Claude product you use — sessions will continue operating as before. Onboarding isn't complete until the agent surface that drives sessions actually reflects the new state.

**Metadata staleness in CLAUDE.md headers.** Mature-project adoption often has tracking metadata (completed/active task lists, current phase, "Last updated" date) at the top of CLAUDE.md that is weeks or months stale by the time of onboarding. When making structural edits to CLAUDE.md for inheritance setup (e.g., adding an "Inherited Principles" section, adding provenance markers to adapted-mode local placements), avoid bumping the "Last updated" date without also reconciling the task tracker. Bumping the date alongside structural changes creates a worse overclaim than the original staleness — it tells future readers the metadata reflects current state. Either fully reconcile the metadata against authoritative current state (which requires separate work) or leave the metadata strictly untouched. Don't half-update.

## What this template doesn't cover

- **Specific operator conventions.** Your operator umbrella may add or specialize phases. Follow your operator's documentation when it differs from this template.
- **Cross-repo access setup.** Each agent surface (planning agents on web, engineer agents in CLI) has its own authorization model. See your operator's cross-repo access setup or the public canonical's operator onboarding for the model.
- **Per-project domain work.** This template covers the structural inheritance setup. Tenant-specific work — engineer prompts for the project's actual work, planning artifacts about the project's substantive domain — is out of scope.

## References

- [`docs/model.md`](./model.md) — the three-concept architecture this template implements.
- [`docs/operator-onboarding.md`](./operator-onboarding.md) — the upstream operator setup that this template assumes is in place.
- [`docs/principles/`](./principles/) — canonical principle text published by this repository.
- Your operator umbrella's `CANONICAL_PRINCIPLES.md` — the authoritative registry of what your tenants inherit and from where.
