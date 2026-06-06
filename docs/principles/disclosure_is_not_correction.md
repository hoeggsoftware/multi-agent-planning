# Disclosure Is Not Correction

**Scope**: This principle applies to both planning agents and engineer agents when committing files to the shared git repository. It governs the commit discipline during any workflow phase where content is being added to the canonical git state.

When an agent is about to commit content known to be incomplete, incorrect, or destructive relative to what should be there, **honest disclosure in the commit message does not redeem the commit**. The destructive effect lands on `main` the moment the commit is pushed. A "STOP — DO NOT MERGE" annotation inside the commit body is a sticky note on a fire after it has burned through the wall: visible only to whoever investigates afterward, by which time the damage is already in the chronicle.

This rule is load-bearing because of git-based coordination. Coordination state IS git state. The state model assumes what lands on `main` is what the author intended to land. Intentionally landing wrong content under cover of an apology breaks the assumption that other agents — and future-self — rely on when reading the repo as authoritative.

## The failure shape

The pattern has a recognizable sequence:

1. Agent recognizes the operation is destructive or degraded.
2. Agent perceives a constraint that makes the correct operation infeasible.
3. Agent decides to ship the degraded version with an honest acknowledgment.
4. The disclosure feels like discipline because it is transparent.
5. The destructive change lands on `main`, and the correction is deferred to "future action" that may or may not happen.

The pattern is contagious across agents. Once one agent ships a known-bad commit with a disclosure, downstream agents reading the file inherit the corrupted state. When asked to do related work, they may treat the prior placeholder as evidence the discipline is acceptable, and ship their own truncation-with-disclosure on top. Chronicles get twice-destroyed this way before anyone realizes the discipline has failed at the seam between agents.

## The rule

If you are about to commit content you know to be incomplete or incorrect relative to what should be present in the file, **stop**. Do not commit-with-apology. Choose one:

- **Test whether the constraint is real.** If the reason you cannot ship the correct version is an assumed tool limitation, attempt the un-degraded operation first and observe the actual failure mode. Discovered limits are binding; imagined limits are not.
- **Hand off to a surface that can do it correctly.** If the current surface genuinely cannot perform the correct operation, surface to the user and request the operation from a surface that can (typically local git for size-bound writes, or operations requiring native rename).
- **Author a paste-ready engineer prompt for cleanup, then stop.** Do not commit the degraded version in the meantime; the prompt is the next action, not a placeholder commit followed by a prompt.

The one thing not to do: commit the degraded version under cover of disclosure and proceed as if the work is in progress. The chronicle does not heal itself; the next agent reading the file inherits whatever you leave on `main`.

## Test-before-defer

The most common imagined constraint is "this file is too large for the tool to handle." This assumption has bitten projects repeatedly. In observed instances, retries without the degradation succeeded — the constraint that felt binding at the moment of decision was not real.

The discipline: when you are about to take a degraded action because of an assumed tool constraint, attempt the un-degraded action first and let the tool tell you whether the constraint is real. The cost of one failed call is bounded. The cost of a placeholder propagating through the chronicle is not.

## If an incomplete file must exist transiently

There are narrow cases where an in-progress file genuinely cannot land in finished form in a single commit — for example, a multi-step migration where intermediate state is structurally required. In those cases, the file must carry an unambiguous marker that no agent reading it can mistake for content-omitted-for-brevity. Use an HTML-comment sentinel at the very top of the file (such as `<!-- INCOMPLETE: do not treat as authoritative; see <ref> -->`) and, where content is missing inline, use a marker syntactically distinct from ordinary markdown punctuation. Bracketed-ellipsis notation like `[... content preserved ...]` reads as ordinary ellipsis-for-brevity and has been mistaken for present-but-elided content in past incidents.

This is a narrow allowance, not a workaround for the rule above. If you find yourself reaching for this section to justify a commit, re-check whether the constraint forcing the incompleteness is real (test-before-defer) and whether handing off to a more capable surface is available.

## Composition with the Hard Evidence Principle

The Hard Evidence Principle says: verify state by direct observation. This rule says: when you are about to write state, do not write known-wrong state on the assumption you will correct it later. The two compose into a single read-write discipline: read the truth, write the truth, verify the truth landed. Disclosure inside a commit message is not part of any of those three steps.

## Applies to engineers and planning agents both

Both surfaces operate against the same git state and have produced this failure mode in observed sessions. The rule applies to engineer commits and planning agent commits alike. Planning agents writing engineer prompts should ensure the dispatch makes the rule explicit when the engineer's work involves rewriting files that have an extensive prior chronicle.
