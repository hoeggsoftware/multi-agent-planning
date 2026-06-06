# Operator onboarding

This guide walks through setting up an operator umbrella — a per-organization layer that consumes from the public canonical (this repository) and curates a registry of principles for your own projects (tenants). If you haven't read [`docs/model.md`](./model.md) yet, start there. This guide assumes the three-concept architecture (public canonical, operator umbrella, tenant project) and focuses on the mechanics.

## When you need an operator umbrella

You need one as soon as you have more than one project that should follow the same disciplines, or when you anticipate having multiple projects. A single project can read principles directly from this repository as part of its session-start reading order; the operator umbrella becomes useful when:

- You have several projects that should inherit consistently — so an update to a principle (yours or upstream's) reaches all of them through a single change in your registry.
- You want to add organization-specific principles that aren't suitable for general publication.
- You want to adapt some public principles locally (restructuring, adding organization-specific elaboration) while passing others through unchanged.
- You want a recorded audit trail of what your organization considers canonical at any given moment.

If you have exactly one project and expect to never have another, you can skip this layer and have your project consume public canonical directly as if it were a tenant of a single-layer "umbrella." But the operator layer is cheap to add and provides room to grow.

## Prerequisites

Before starting, confirm:

- **GitHub organization (or personal account)** under which the operator umbrella will live. The operator repo can be private or public; private is typical because operator content includes governance specifics, tenant lists, and implementation history that you probably don't want to publish.
- **Permission to install GitHub Apps** on the organization. Both apps (described below) need to be installed for end-to-end access to work.
- **Familiarity with the public canonical**'s current principles. Read [`docs/principles/`](./principles/) to know what you'll be inheriting. You don't have to inherit everything; the registry lets you pick.
- **A working understanding of git as the coordination substrate.** This model assumes git state is the coordination state — prompts, principles, inheritance indexes are all committed files.

You do not need any custom tooling installed. The operator umbrella is markdown documents in a git repo. Existing GitHub workflows, MCP-based agent tools, and `gh` CLI cover the operational needs.

## Step 1: Create the operator repository

Create the operator repository under your organization. Suggested name: something that reflects its role, like `<your-org>/planning-ops`, `<your-org>/agent-coordination`, or whatever conveys "this is the coordination layer." Visibility is typically private.

Recommended initial structure:

```
<your-org>/<your-operator-repo>/
├── CLAUDE.md                           # Orientation for agents working in this repo
├── docs/
│   ├── agents/
│   │   ├── INHERITED_PRINCIPLES.md     # Your inheritance from public (Step 2)
│   │   ├── CANONICAL_PRINCIPLES.md     # Your registry (Step 3)
│   │   ├── tenant_onboarding_checklist.md  # Adapted from public template
│   │   └── PLANNING_AGENT_PROMPT_GUIDELINES.md  # Optional; may inherit public PAG
│   ├── principles/                     # Your operator-originated principles (Step 4)
│   ├── architecture/                   # Operator-specific architecture docs
│   └── proposals/                      # Operator's own design history
├── implementation_logs/                # Completed work record
└── <other operator-specific directories>
```

This is a recommended layout, not a required one. The load-bearing files are `docs/agents/INHERITED_PRINCIPLES.md` and `docs/agents/CANONICAL_PRINCIPLES.md`. Everything else is operator discretion.

The operator's `CLAUDE.md` should orient agents to your operator's specific setup — what tenants you have, what conventions you follow, what cross-component patterns matter. There's no template for this because operator setups vary; the key is that it serves as the entry point for any planning-agent session against your operator repo.

## Step 2: Inherit from public canonical

The operator umbrella is itself a tenant of public canonical. Like any tenant, it records its inheritance in a `docs/agents/INHERITED_PRINCIPLES.md` file.

For each principle in this public canonical you want to expose to your tenants:

1. Decide the **inheritance mode**: reference (default) or adapted.
2. If reference mode: record the principle name, mode `reference`, source path (e.g., `hoeggsoftware/multi-agent-planning/docs/principles/<name>.md`), and the public canonical's current SHA at the moment of your inheritance setup. This SHA is a last-verified marker, not a current pin — drift between recorded SHA and current public canonical is normal for reference mode.
3. If adapted mode: author your local adapted version (typically at `docs/principles/<name>.md` in your operator repo, with a provenance marker pointing at public canonical at the specific SHA). Record in INHERITED_PRINCIPLES.md: name, mode `adapted`, source path, source SHA at last sync, local location, and one-line adaptation rationale.

You don't have to inherit every principle the public canonical publishes. Inherit only what makes sense for your organization. The decision is per-principle.

If you adapt a principle locally, the adaptation rationale should be a one-line description of why the local form differs structurally — e.g., "Adds operator-specific multi-repo asymmetry caveat to clarify how the principle applies when planning and engineer surfaces have different cross-repo access patterns." The rationale documents your decision for future you and for any agent reading the inheritance index.

## Step 3: Author the operator's registry

Your operator's `docs/agents/CANONICAL_PRINCIPLES.md` is the registry that declares what your tenants inherit. Its column shape (per the [model](./model.md)):

| Column | Description |
|---|---|
| **Name** | Principle name as exposed to tenants |
| **Origin** | `upstream`, `adapted-upstream`, or `self` |
| **Canonical source** | Path resolving per Origin |
| **Canonical SHA** | SHA of the canonical content the operator has acknowledged |
| **Status** | `active`, `superseded`, or `deprecated` |
| **Tenant inheritance** | Per-tenant mode declarations |

For each principle you inherit from public:

- If you inherit in reference mode, the row gets `Origin=upstream`, Canonical source pointing at the public path, Canonical SHA matching what's in your INHERITED_PRINCIPLES.md. No operator-local file needed.
- If you inherit in adapted mode, the row gets `Origin=adapted-upstream`, Canonical source pointing at your local file, Canonical SHA matching your local file's commit. The operator-local file at `docs/principles/<name>.md` is your adapted version.

Operator-originated principles (Step 4 below) get `Origin=self`, Canonical source pointing at your local file, Canonical SHA matching the file's commit.

The **Tenant inheritance** column starts empty — you add to it as you onboard your first tenants.

The Canonical SHA column is what tenants pin against when they inherit in adapted mode. Treat it as mandatory: when you advance the canonical content, advance the SHA in the same commit. Tenants depend on it.

## Step 4: (Optional) Author operator-originated principles

If your organization has disciplines that aren't covered by public canonical, author them as operator-originated principles. They live at `<your-operator-repo>/docs/principles/<name>.md` in the same file shape as public principles (title + scope note + canonical text).

Register them in `CANONICAL_PRINCIPLES.md` with `Origin=self`, Canonical source pointing at the local file, and the file's commit SHA.

If an operator-originated principle later becomes generally useful, it's a candidate for upstreaming to public canonical (via a PR to this public repo per [`CONTRIBUTING.md`](../CONTRIBUTING.md)). Once accepted, your registry row may transition to `Origin=upstream` (passthrough), `Origin=adapted-upstream` (you keep restructuring), or simply note the upstream successor in your registry's changelog.

It is also valid to have no operator-originated principles. Many operators start with just upstream principles and add operator-originated ones only when a specific need emerges.

## Step 5: Configure cross-repo access for your agent surfaces

This is the step that most often produces surprises. Multi-agent workflows touching multiple repos (your operator, your tenants, public canonical) need access configured per agent surface. The model assumes you'll be using Claude as the agent toolkit; if you use something else, adapt accordingly.

### For agents on claude.ai web (planning surface)

These agents authenticate through GitHub's OAuth flow via Anthropic's hosted MCP connector ("Claude Github MCP Connector" GitHub App). The grant is OAuth-based and spans accessible orgs without requiring per-org installation. Configure:

- Authorize the "Claude Github MCP Connector" app for your GitHub account via the connector setup in claude.ai.
- Once authorized, the connector can read from public repos (this canonical repo) automatically.
- For private operator and tenant repos, also add each repo to the "Claude Github MCP Connector" installation's selected repositories on the org page (`https://github.com/settings/installations` → Configure on the "Claude Github MCP Connector" installation → add the operator and tenant repos to selected repos).

### For agents in Claude Code (engineer surface)

These agents authenticate through the "Claude" GitHub App. Per-installation, per-account scoped — installation must be explicitly done on each org with explicit per-repo grants. Configure:

- Install the "Claude" GitHub App on your operator org and on any org that hosts your tenants.
- For each installation, add the operator repo and tenant repos to the installation's selected-repos list.
- Cross-org access works only when the app is installed on each org separately. Granting access to `<org-a>/repo-x` does not automatically grant access to `<org-b>/repo-y` even if the user has read access to both.

### Fallback path: gh CLI

The `gh` CLI authenticates through a personal access token (`gh auth login`) and bypasses GitHub Apps entirely. It has access to anything the user can see as a human. Useful as a fallback when MCP returns 404 for installation-related reasons — engineers can run `gh api repos/<owner>/<repo>/contents/<path>` for cross-repo reads when MCP fails.

### When access fails

The diagnostic sequence:

1. **404 from GitHub MCP?** Likely an installation issue. Check `https://github.com/settings/installations`: is the target repo's owning account on the installed list, and is the target repo in that installation's selected-repos list? Install or grant as needed.
2. **403?** Likely an OAuth scope issue or an org policy restricting third-party apps. Check the OAuth grant for the relevant app and (for org repos) check the org's third-party-app policies.
3. **Intermittent access?** Possible token expiration; re-auth via `gh auth login` for the CLI path, or revoke and re-grant the OAuth app for the MCP path.
4. **Engineer blocked by MCP?** Fall back to `gh` CLI.

This authorization model applies to public, operator, and tenant repos. Public read-only access is necessary but not sufficient — the agents that edit your operator and tenant repos need write grants on those repos, not just public read access.

## Step 6: Verify the setup end-to-end

Before onboarding your first tenant, exercise the operator setup with a planning-agent session:

- Open a session pointed at the operator repo (via your preferred Claude surface).
- Confirm the session can read the operator's `INHERITED_PRINCIPLES.md`, `CANONICAL_PRINCIPLES.md`, and (if you authored any) operator-originated principles.
- Follow the source paths in `INHERITED_PRINCIPLES.md` and confirm the session can read the public canonical principle files via the MCP connector.
- For any adapted-mode entries, confirm both the local adapted version and the public canonical source are readable, and that the local version reflects current public canonical content per the recorded SHA.

If reads fail at this stage, the most likely cause is missing grants per Step 5. Resolve those before moving on.

## Step 7: Onboard your first tenant

Once the operator umbrella is in place, you can onboard tenant projects. See [`docs/tenant-onboarding.md`](./tenant-onboarding.md) for the per-tenant template.

The operator umbrella's role in tenant onboarding:

- Your `tenant_onboarding_checklist.md` (adapted from the public template per Step 1's recommended structure) guides per-tenant work, with operator-specific conventions encoded.
- After each tenant is onboarded, update your `CANONICAL_PRINCIPLES.md`'s Tenant inheritance column to add the tenant's mode declarations.
- Maintain a changelog at the bottom of `CANONICAL_PRINCIPLES.md` recording tenant additions and inheritance mode changes over time.

## Ongoing maintenance

Once the operator umbrella is operating, four maintenance patterns recur:

### Public canonical updates

When public canonical's principle text changes:

- Reference-mode rows (`Origin=upstream`): your operator picks up the new content automatically at next session-start read. You may bump the Canonical SHA in your registry as an acknowledgment marker; this is operator discretion (some operators prefer the registry to record only acknowledged content, others let it lag behind verified-at-onboarding).
- Adapted-upstream rows (`Origin=adapted-upstream`): the public canonical changed but your local adapted version hasn't yet. You decide whether to re-translate the change into your local version (then bump your INHERITED_PRINCIPLES.md source SHA and your CANONICAL_PRINCIPLES.md Canonical SHA) or to stay pinned at the older content.

The translation IS the sync — bumping SHA without translating the content breaks the audit trail. If you bump the SHA, the local content must reflect the new canonical state.

### Origin changes

If you change a row's Origin (e.g., a previously-upstream principle becomes adapted-upstream because you've decided to restructure it locally, or a self-originated principle gets upstreamed to public canonical and you transition to upstream-passthrough):

1. Update the Origin, Canonical source path, and Canonical SHA in your CANONICAL_PRINCIPLES.md.
2. Update your INHERITED_PRINCIPLES.md to reflect any changes to your operator's inheritance from public.
3. Notify and dispatch updates to all affected tenants (Tenant inheritance column tells you which ones). Tenants need to update their recorded source paths to match your new declaration. Reference-mode tenants' new Source SHA is your new Canonical SHA as the new last-verified marker. Adapted-mode tenants port per standard adapted-mode discipline and decide whether their local placement still applies.

Origin changes are not common but they're not edge cases either. Make them deliberately and propagate cleanly.

### Public canonical removal or deprecation

If public canonical removes or deprecates a principle your operator inherits, you face a decision:

- **Pin and fork**: transition the row from `Origin=upstream` to `Origin=self`. Take ownership locally. Your `docs/principles/<name>.md` is now operator-authored (content can be the historical public version forked at the deprecation SHA, or evolved from there).
- **Migrate to successor**: if public deprecation names a successor principle, update your row to point at the successor.
- **Demote and remove**: if you no longer need the principle either, demote (`Status=deprecated`) and eventually remove from your registry. Notify tenants to remove their inheritance.

Don't silently leave `Origin=upstream` rows pointing at deprecated public content. The discipline catches this if you periodically verify your Canonical SHA against current public state.

### Adding tenants over time

For each new tenant: use your `tenant_onboarding_checklist.md`, then update CANONICAL_PRINCIPLES.md's Tenant inheritance column and changelog.

## What this guide doesn't cover

- **Specific tenant onboarding mechanics** — see [`docs/tenant-onboarding.md`](./tenant-onboarding.md).
- **The model itself** — see [`docs/model.md`](./model.md).
- **Principle content** — see [`docs/principles/`](./principles/) for the published principles' text.
- **Contributing to public canonical** — see [`CONTRIBUTING.md`](../CONTRIBUTING.md).

If you encounter situations this guide doesn't address — for example, multiple public canonicals, deeply nested operator hierarchies, or complex multi-org topologies — open an issue against this repository. The model has been used in production for the scenarios described here; more elaborate scenarios surface gaps in the model that may warrant new versions.
