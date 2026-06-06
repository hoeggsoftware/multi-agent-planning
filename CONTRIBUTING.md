# Contributing

This repository is in early days, and the contribution policy is intentionally minimal at this stage. The policy will evolve with experience and as outside contributions surface specific patterns worth governance.

## What you can contribute

- **Principle additions.** A new principle file under `docs/principles/` following the file shape described in [`docs/principles/README.md`](./docs/principles/README.md) (title + scope note + canonical text). Open an issue first to discuss whether the principle is a fit for this repository's scope.
- **Principle revisions.** Changes to existing principle text. Substantive changes warrant an issue first; small clarifications can go straight to a pull request.
- **Model documentation improvements.** Clarifications, corrections, or additions to [`docs/model.md`](./docs/model.md), [`docs/operator-onboarding.md`](./docs/operator-onboarding.md), or [`docs/tenant-onboarding.md`](./docs/tenant-onboarding.md).
- **Operator or tenant pattern observations.** If your operator umbrella or tenant project surfaces an interesting pattern that might apply more broadly, file an issue to discuss whether it's principle-shaped (warrants a new principle file) or pattern-shaped (worth capturing in the onboarding guides).

## How to contribute

- **Issues**: open one against this repository to start a conversation. Issues are also the right place to ask questions about the model, the principles, or the onboarding guides.
- **Pull requests**: small, focused PRs are easier to review than large ones. PRs to principle files specifically should explain the substantive case in the PR description, since changes to canonical text affect every adopter.

## Scope

This repository's scope is the canonical principles and the multi-agent planning model that organizes their reuse. Contributions that fit cleanly within that scope are most likely to land. Contributions that would expand the scope — for example, proposing tooling that codifies the model, additional layer concepts, or formal verification — warrant explicit discussion first because they reshape what this repository commits to maintaining.

## Maintainer

This repository is maintained by Hoegg Software. The maintainer reviews and merges contributions; major decisions about scope, principle inclusion, or model evolution rest with the maintainer.

## License

By contributing, you agree that your contributions will be licensed under the Apache 2.0 license that covers this repository (see [`LICENSE`](./LICENSE)).
