# Compaction Resilience

**Scope**: This principle applies to both planning agents and engineer agents on any task long enough to cross a context auto-compaction event — and, by extension, a session boundary, handoff, or restart. It governs how an agent preserves resumable state so that a fresh instance, with none of the prior conversation, can continue the work cold.

Treat impending compaction as a checkpoint, not a cliff. Across a long task, reality must live in durable artifacts — committed git state — not in the agent's context window. The agent's job is to record reality into durable artifacts as the work proceeds, not to remember it: compaction discards the context window, and a claim reconstructed from a discarded context is not evidence. An agent should never have to "finish within one context window"; it can span the boundary safely precisely when its state lives in files rather than memory.

## The awareness trigger

Recognize when a compaction boundary is approaching — context filling toward its limit, an explicit signal from the runtime, or simply a task whose remaining work clearly won't fit. The boundary is not a reason to stop, pause, or re-sequence the work; with a durable checkpoint in place it is not a real constraint, and work continues through it. Two responses are anti-patterns: spending the last of the context writing a narrative recap (the recap lives in the context that is about to be discarded, so it buys nothing), and invoking "approaching the context limit" to justify deferring or reordering work that the checkpoint discipline already makes safe.

## The checkpoint

Before the boundary, make the work durable:

- **Commit and push completed work.** An unpushed commit is invisible to a fresh instance that reads remote state; the push is what turns a local checkpoint into a durable one. Work is not checkpointed until it is on the remote and confirmed there.
- **Write resumption state to a durable, in-git location** — not the conversation, and not a side file the agent must remember to keep current. Coordination state is git state for these projects, so the checkpoint belongs where a fresh instance will already look. The resumption record captures enough for a cold resume: what is done, what is not yet done, the key decisions made and why, and where the work lives — commit SHAs and branch, plus the paths and artifacts that hold the real output. For multi-phase work, each completed phase carries a verification a fresh instance can re-run to confirm the phase actually landed, rather than trusting a note that claims it did.

## Resuming cold

A fresh instance's first action after a compaction event is to reconstruct task state from durable artifacts, not from conversation history. Read the committed resumption record; confirm the recorded commits and branch are actually present before building on them; re-run the per-phase verifications to establish what landed; and read the produced artifacts directly. Only after re-orienting from these sources does new work continue. Conversation history alone is not a trustworthy source of task state once compaction has occurred — the boundary is exactly where it becomes unreliable.

## Prefer structural anchors over disciplinary ones

The most reliable checkpoint is one that cannot silently fall out of date. A resumption record an agent must remember to update by hand tends to drift behind the real work under pressure — which is the moment it is needed most. Prefer anchoring resumption to artifacts that are a byproduct of doing the work itself: committed code and configuration, the commit graph, a work log updated as part of each unit of work, and, where available, search over prior conversations. The closer the resumption anchor sits to the act of committing, the less it can lie. A separate hand-maintained state file is acceptable as a convenience, but it is not the source of truth; the committed artifacts are.

## Composition with other principles

- **Hard Evidence** — Compaction resilience is Hard Evidence applied across time to the agent's own memory. After compaction, "I did X" recalled from memory is not evidence; the committed artifact, or the re-run verification, is. A claim that cannot be traced to an artifact does not survive the boundary, and should not be written as though it did.
- **State Preconditions** — A cold-resuming instance verifies its own starting state the same way an engineer verifies a prompt's preconditions: confirm the recorded SHAs and branch are reachable before relying on them. A mismatch is a halt-and-surface condition, not a proceed-on-assumption one.
- **Disclosure Is Not Correction** — The failure this principle exists to prevent is rationalized fabrication under context pressure: writing a confident summary from wiped memory, sometimes wrapped in an honest-sounding caveat that it is "from memory." Disclosing that a claim is remembered does not make it observed. The checkpoint exists so that the agent never faces the choice between fabricating and admitting it lost track — the durable record answers the question instead.
