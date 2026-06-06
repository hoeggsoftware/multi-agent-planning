# Documentation-First Discipline

**Scope**: This principle applies to both planning agents and engineer agents when encountering obstacles or unexpected behavior during task execution. It governs the initial response to problems before attempting solutions or variations.

When you hit an obstacle whose shape suggests "infrastructure or process I expected to work doesn't," your first move is to consult project documentation before iterating with variations or asking for help. Multi-agent projects invest effort capturing operational knowledge in architecture docs, procedure docs, lessons-learned files, and state files specifically so future agents don't re-discover the same obstacles.

## When this applies

- **Access or connectivity fails unexpectedly** → check procedure documentation first for the established access patterns. Connection failures, authentication issues, and network reachability problems often have documented causes and solutions specific to the project's infrastructure.

- **Configuration appears missing fields or behaves unexpectedly** → check architecture documentation for the relevant component's design. Configuration intent is captured there, including non-obvious decisions about structure, defaults, and field relationships.

- **A status or milestone seems incorrect** → check the relevant state files in the project. Mental snapshots drift; committed state files are authoritative.

- **Something is described as "operational" or "complete" but doesn't behave that way** → check status documentation and lessons-learned files for status-correction notes. Projects often capture instances where claimed-complete didn't match actual-state.

- **A behavior or pattern surprises you** → check lessons-learned documentation for observations about recurring patterns. Patterns across past work may already be documented.

- **A tool or command fails and you're considering trying variations** → check the tool's official documentation AND any project-specific guidance for that tool before iterating. Variations chosen in ignorance of documented patterns rarely succeed.

- **You see a specific error message you don't recognize** → before inventing a hypothesis, search the documentation directory for the error string. Many procedure docs include verbatim error messages specifically so this search is fast.

## The discipline

When stuck, the first 5 minutes go to checking documentation, not to trying variations. Trying variations without checking docs that document the exact failure mode wastes time and produces the same wrong conclusions repeatedly. Projects often have captured instances where agents iterated for 30+ minutes on a problem that was documented in 5 minutes of reading.

## The signal that this principle applies

When you find yourself saying "let me try a different approach," "let me try a different command," or "let me try X instead" — pause. Have you read the relevant procedure or architecture doc? Variations chosen in ignorance of documented patterns rarely succeed and often compound into multi-step deviations from working approaches.

## Composition with the Hard Evidence Principle

The Hard Evidence Principle says "verify before concluding"; Documentation-First Discipline says "read before iterating." Together they form the operational discipline: read what the project knows, then verify by direct observation before drawing conclusions.

## For planning agents

Anticipate which documentation will be relevant to each task and list it explicitly in the prompt's "Context to Read" section. The planning agent's job includes pre-identifying the documents that save the engineer from rediscovering known patterns. This is especially important for tasks that touch infrastructure, access patterns, or components with non-obvious operational characteristics.
