# Multi-agent planning

A system for sharing agent-coordination disciplines across projects.

Teams running multi-agent AI workflows develop disciplines for coordination — verifying claims by direct observation, preserving prompt-authoring discipline across agent handoffs, treating committed git state as the source of coordination truth, consulting documentation before iterating on obstacles. The disciplines look similar across projects; reinventing them per-project is friction. This repository packages them as canonical principles, plus an inheritance model that lets organizations reuse those principles across their own projects.

The principles themselves are markdown documents. The "system" is the inheritance index plus a small set of conventions for how layers compose (public canonical / operator umbrella / tenant project). There is no runtime framework, no library to install, no tooling to maintain. The discipline content travels via standard git workflows.

## What's here

- [`docs/principles/`](./docs/principles/) — canonical principle files. Each is the authoritative statement of one discipline.
- [`docs/model.md`](./docs/model.md) — the three-concept architecture (public canonical, operator umbrella, tenant project) and the inheritance modes (reference and adapted).
- [`docs/operator-onboarding.md`](./docs/operator-onboarding.md) — how to set up an operator umbrella for your organization.
- [`docs/tenant-onboarding.md`](./docs/tenant-onboarding.md) — template for onboarding a project as a tenant under an operator umbrella.
- [`docs/agents/PLANNING_AGENT_PROMPT_GUIDELINES.md`](./docs/agents/PLANNING_AGENT_PROMPT_GUIDELINES.md) — conventions for planning agents authoring engineer prompts. Operators typically inherit these as the basis for their own planning-agent discipline.
- [`CONTRIBUTING.md`](./CONTRIBUTING.md) — contribution policy.

## Where to start

**Discovering the project.** Read [`docs/model.md`](./docs/model.md) to understand what's here and how the layered architecture works. Then browse [`docs/principles/`](./docs/principles/) to see what disciplines this canonical publishes.

**Adopting the model for your organization.** Read the model overview, then follow [`docs/operator-onboarding.md`](./docs/operator-onboarding.md) to set up your own operator umbrella. The operator layer is per-organization; you create one operator umbrella against this public canonical, then onboard your projects as tenants of your operator.

**Setting up a single project under an existing operator umbrella.** Use [`docs/tenant-onboarding.md`](./docs/tenant-onboarding.md) as a template. Your operator umbrella may have its own checklist derived from this template with operator-specific conventions; follow that if it exists.

**Contributing to this repository.** See [`CONTRIBUTING.md`](./CONTRIBUTING.md). Principle additions, model documentation improvements, and observations about operator or tenant patterns are all in scope. Open an issue to discuss substantive changes before sending a PR.

## What this isn't

This repository is not a runtime framework. The principles describe disciplines that apply to agent coordination work regardless of which AI assistant or toolkit you use. The model organizes how those principles propagate across projects; the principles themselves stand on their own merit.

This repository is also not specific to any single agent product. The principles are written generically enough to apply to coordination involving multiple agents around shared artifacts — human-and-AI collaboration, AI-and-AI handoffs, multi-surface workflows. The examples and operational specifics in the onboarding guides reference common patterns (GitHub-hosted git, MCP-based tool access, CLI-based engineer surfaces) because those are common substrates today, not because the model requires them.

## License

[Apache 2.0](./LICENSE).

## Maintainer

Hoegg Software.
