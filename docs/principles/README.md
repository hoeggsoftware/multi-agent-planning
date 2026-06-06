# Canonical principles

This directory holds the canonical text of agent-coordination principles published by this repository. Each file is the authoritative statement of one principle.

## File shape

Each principle file contains, in order:

- **Title** matching the registered principle name.
- **Scope note** (one paragraph): who reads this principle and when it applies. Brief context to orient a reader who encounters the file standalone.
- **Canonical text**: the principle as it should be applied. Generic framing — no specific operator or tenant examples; the principles are written to be reusable across many adopter contexts.

That is the entirety of the file shape. Rationale, change history, and adopter-specific elaborations live elsewhere — in adopter operator umbrellas, in tenant project docs, or in pull requests proposing changes to a principle.

## Current principles

- [`disclosure_is_not_correction.md`](./disclosure_is_not_correction.md) — committing known-bad content with a disclosure does not redeem the commit; choose test-the-constraint, hand-off, or cleanup-prompt instead.
- [`documentation_first.md`](./documentation_first.md) — when blocked, consult project documentation before iterating with variations.
- [`hard_evidence.md`](./hard_evidence.md) — base decisions on direct observation, not assumptions or memory.
- [`state_preconditions.md`](./state_preconditions.md) — engineer prompts referencing repository state open with a precondition block specifying base and dispatch commits, referenced commits, and file-state fragments.

## How adopters use these principles

Adopters typically apply these principles through the multi-agent planning model's layered architecture rather than referencing them directly. The model factors adoption through an operator umbrella layer that consumes from this repository, curates a registry of which principles its tenants inherit, and allows local adaptation where context demands it. See [`docs/model.md`](../model.md) for the architecture and [`docs/operator-onboarding.md`](../operator-onboarding.md) for the setup mechanics.

For a single project that wants to use these principles without setting up an operator umbrella, the project's reading order can reference this directory directly — treating the inheritance index as a degenerate case with one consumer. The operator layer becomes worth setting up when you have multiple projects that should follow the same disciplines.

## Proposing a new principle or revision

See [`CONTRIBUTING.md`](../../CONTRIBUTING.md) for the contribution policy. Principle changes warrant explicit discussion because adopters depend on the canonical text as the authoritative statement; substantive revisions should open an issue first to discuss the change.
