# State Preconditions for Engineer Prompts

**Scope**: This principle applies to planning agents authoring engineer prompts and to engineer agents receiving those prompts. It governs the handoff discipline when dispatching work that depends on specific repository state.

Any engineer prompt — whether committed under `active/{category}/` or pasted into chat for delivery — that references specific repository state must open with a precondition block. The precondition is the engineer's first verification step: confirm the local checkout matches the planning agent's expected state before proceeding.

## Why preconditions exist

Multi-agent coordination depends on a shared substrate (git state), but the agents themselves do not share a single view of that substrate. A planning agent operating on web-based tools may read repository state against the remote. An engineer in a local CLI environment reads a local checkout that may be behind, ahead, or on a different branch. When the planning agent describes "the file currently looks like X" and the engineer's local checkout shows "the file currently looks like Y," the engineer must distinguish three cases:

- The planning agent's view is stale and the engineer should proceed against local state.
- The engineer's view is stale and the engineer should fetch before proceeding.
- Both views are current but describe different branches.

Without an explicit precondition, the engineer's default is to trust local state, which produces "the work is already done" or "the commits this prompt references do not exist" misreadings when the local checkout is behind. The engineer is not wrong to trust their tools; the prompt is wrong to omit the gate that makes the trust verifiable.

## What to include

At minimum:

- **Base commit SHA** — the SHA of the main branch at the time the planning agent authored the prompt. This is the audit trail for "the prompt was written against this state." For prompts committed under `active/`, this is normally the parent of the dispatch commit; for paste-delivered prompts, no separate dispatch commit exists.
- **Dispatch commit SHA** (optional, added by the planning agent after commit-and-push) — the SHA of the commit that adds the prompt to the main branch. Useful for engineers fetching after dispatch, whose remote HEAD will be this commit rather than the base.
- **Specific commits the prompt references**, by SHA, with one-line descriptions, so the engineer can confirm they are present (or absent) in their local view.
- **Identifying content fragments** the engineer can grep for to confirm file state — short distinctive strings that reveal whether a file is in the expected state, not full content reproductions.

The engineer's first action upon receiving the prompt: confirm each precondition.

The halt condition for the SHA preconditions: **after fetching from the remote, neither the base commit nor the dispatch commit is reachable in the engineer's local git log**. If either resolves, the SHA precondition is satisfied — the base commit confirms the prompt's authored state is part of the local history; the dispatch commit confirms the prompt itself is in the local clone. File-state preconditions are evaluated independently.

Any mismatch after fetch is a halt-and-surface condition, not a "proceed with local state" condition.

## Format

A precondition block belongs immediately after the prompt's title, before Objective. Example shape:

```
## Preconditions — verify before proceeding

Expected state at dispatch time:
- Base commit (prompt authored against): <SHA>
- Dispatch commit (adds this prompt to main): <SHA>  [optional; planning agent fills in after commit]
- Recent commits referenced in this prompt:
  - <SHA> — <one-line description>
  - <SHA> — <one-line description>
- Expected file state:
  - <path>: contains the string "<distinctive fragment>"
  - <path>: does NOT contain the string "<fragment that would indicate already-done state>"

Verification: after fetching, confirm that EITHER the base commit OR the dispatch
commit is reachable in git log. If neither resolves, halt and surface to the
planning agent before proceeding. File-state preconditions are evaluated
independently of the SHA check.
```

## Marker syntax for state fragments

When citing fragments the engineer should grep for, prefer distinctive strings the file would only contain in one specific state. Avoid generic markdown punctuation that the engineer might interpret as content-omitted-for-brevity rather than literal file content. When a fragment is itself a marker indicating an incomplete file, make the marker syntactically distinct from ordinary prose.

## Paste-ready chat prompts

Paste-ready engineer messages delivered in chat are subject to the same discipline. The precondition block belongs at the top of the paste-ready content, before any narrative or task description. The planning agent authoring the paste-ready prompt is responsible for capturing the expected state at the moment the prompt is composed, not relying on the engineer to reconstruct it.
