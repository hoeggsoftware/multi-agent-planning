# The multi-agent planning model

A system for sharing agent-coordination disciplines across projects.

This repository hosts canonical principles for coordinating AI agents working on shared artifacts via git, plus an inheritance model that lets organizations reuse those principles across their own projects. The principles themselves capture disciplines like verifying claims by direct observation rather than assumption, treating committed git state as the source of coordination truth, and preserving prompt-authoring discipline across agent handoffs. The inheritance model lets those disciplines apply consistently to many projects without each one having to maintain its own copy.

## Why a model, not just a list of principles

A team running a single project can adopt a discipline by reading a doc and applying it. A team running several projects — or several teams sharing an organization — needs the discipline to apply consistently across all the projects, with a way to update one canonical version and have the change reflect everywhere. They also need room to add organization-specific patterns that don't make sense to publish, and to adapt published patterns locally when their context demands it.

The model factors these concerns into three layers. Each layer has a clear role; the boundaries between them are explicit.

## The three layers

### Public canonical source

This repository. It contains canonical principles, this model documentation, and guides for setting up the layers below. It is owned by the original publisher and intended for reuse.

The public canonical does not know who consumes it. It maintains no registry of organizations using the model. It defines the principles and the model; the layers below decide how to apply them.

### Operator umbrella

A per-organization repository that consumes from the public canonical and curates a registry of principles for its own projects. Each adopting organization creates one of these as their own coordination layer.

An operator umbrella does several things at once:

- It records what it inherits from public, in what mode (see Inheritance modes below).
- It may author operator-originated principles that meet its specific needs but aren't suitable for general publication.
- It may hold locally-adapted versions of public principles when its context demands restructuring.
- It maintains a registry that declares, for each principle, what the canonical source currently is.

The operator umbrella is the authority for its tenants. When a tenant inherits a principle, the path it records resolves to wherever the operator's registry currently points — which may be the public canonical, an operator-local adapted version, or an operator-originated file.

An operator umbrella is itself a tenant of the public canonical. It inherits from public the same way its own tenants inherit from it. This recursive structure is what lets the model generalize: the rules apply identically at both layers.

### Tenant project

A working project — a research effort, a client engagement, an internal tool, a personal initiative. It points at its operator umbrella for inheritance and focuses on its domain.

A tenant project records which principles it inherits from its operator umbrella, in what mode, and may add tenant-local principles that are canonical to that project and not shared.

Tenants do not typically have sub-tenants. The model scopes to two layers of inheritance: public above operators, operators above tenants.

## How the layers compose

The simplest case — a tenant inheriting a public principle in reference mode through an operator that also inherits it from public in reference mode:

```
public canonical: docs/principles/<name>.md (the canonical text)
       ↑
       │ operator inherits from public in reference mode
       │
operator umbrella registry: declares this principle as Origin=upstream, canonical source = public path
       ↑
       │ tenant inherits from operator in reference mode
       │
tenant CLAUDE.md (or similar): reading order includes public/.../canonical.md
```

The tenant's reading order points at the public canonical path because that's what the operator's registry declares. The operator's registry is the authority; the tenant's recorded path is a snapshot of that authority at the tenant's last sync.

A more involved case — an operator adapts a public principle locally, then exposes its adapted version to tenants:

```
public canonical: docs/principles/<name>.md
       ↑
       │ operator inherits in adapted mode (provenance marker pointing at public)
       │
operator umbrella: docs/principles/<adapted-name>.md (operator's adapted version)
       ↑
       │ operator registry declares this principle as Origin=adapted-upstream
       ↑
       │ tenant inherits in reference mode
       │
tenant reading order: includes operator/.../adapted-name.md
```

The operator did the adapting; tenants read the operator's adapted version directly. Each layer's mode is independent: an operator can be in adapted mode while its tenants are in reference mode, and vice versa.

## Inheritance modes

A consumer (operator-as-tenant-of-public, or tenant-of-operator) inherits each principle in one of two modes:

**Reference mode.** The consumer reads the canonical text directly at session start; no local copy. Lowest maintenance — when canonical content changes, the consumer picks it up automatically. The consumer's inheritance index records a path and the SHA at last verification (useful as an audit marker, not as a current pin — drift between recorded SHA and current canonical is normal).

**Adapted mode.** The consumer holds a local restructured version of the canonical content, with a provenance marker pointing at the canonical source and a SHA pin recording which canonical version the local content reflects. Used when local restructuring or context-specific elaboration is genuinely needed. Higher maintenance — when canonical content changes, the local version must be re-translated.

Default to reference mode. Choose adapted mode only when restructuring is justified.

## The operator's registry

The operator umbrella's registry — `docs/agents/CANONICAL_PRINCIPLES.md` — is the structural piece that makes mix-and-match possible. Each row declares one principle the operator exposes to its tenants, with these columns:

| Column | Description |
|---|---|
| Name | Principle name |
| Origin | Where the canonical lives: `upstream` (public), `adapted-upstream` (operator-local adapted version of public), or `self` (operator-originated) |
| Canonical source | Path resolving per Origin |
| Canonical SHA | The canonical content SHA the operator has acknowledged |
| Status | `active`, `superseded`, or `deprecated` |
| Tenant inheritance | Per-tenant mode declarations |

The Origin column is the load-bearing innovation of this model. It distinguishes principles whose canonical source is upstream (public) from those originated locally by the operator from those operator-adapted from public content. This is what enables an operator to adopt three public principles, replace one with their own version, and add two operator-originated principles for their specific topology — and to record those decisions explicitly so they can evolve over time.

## Origin vs. Mode

These two concepts both involve the word "adapted" but describe different things:

| | What it describes | Lives in |
|---|---|---|
| **Origin** | Where the operator's tenant-facing canonical comes from | Operator's registry |
| **Mode** | How a consumer (operator or tenant) uses the canonical they inherit | Consumer's inheritance index |

The contrast that most easily confuses: an operator with `Origin=adapted-upstream` (the operator adapted the public version, that's its tenant-facing canonical) is different from a tenant with `Mode=adapted` (the consumer holds a local restructured version of the canonical they inherit). Both involve adaptation; they're at different layers.

## What this isn't

This model is not a runtime framework or a library. It's a discipline pattern that organizes how teams reuse coordination principles. The artifacts are markdown documents in git repositories. The "system" is the inheritance index plus a small set of conventions for how layers compose.

The principles published here are also not specific to any one AI assistant or agent toolkit. They describe disciplines (hard evidence, documentation-first, preconditions for handoffs, commit discipline) that apply to any coordination involving multiple agents around shared work artifacts. The model just gives those disciplines a portable container.

## Getting started

Two starting points depending on your role:

- **You're starting a new project** and you want to adopt this model: see [`docs/tenant-onboarding.md`](./tenant-onboarding.md). The template guides you through setting up your project as a tenant of an existing operator umbrella.

- **You're setting up an operator umbrella** for your organization: see [`docs/operator-onboarding.md`](./operator-onboarding.md). It walks through creating your operator repository, configuring its registry, and inheriting from public canonical.

The principles themselves are in [`docs/principles/`](./principles/). Read them to understand what disciplines you'd be adopting before deciding how to adopt them.
