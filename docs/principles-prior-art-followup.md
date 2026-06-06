# Principles prior-art scan: focused follow-up

> **What this is.** A deliberately narrow follow-up to [`docs/principles-prior-art.md`](./principles-prior-art.md),
> reading five specific sources deeply rather than surveying broadly: one peer work (the `how-to-plan`
> skill in `simiancraft/simiancraft-skills`) and four methodological traditions (the Agile Manifesto,
> Lean Thinking, Deming's TQM, and the Theory of Constraints). Its job is to deepen the evidence behind
> the two refinement candidates scan 2 flagged — postcondition-symmetry and the irreversibility gate —
> and to give the peer work a full, fair evaluation. Conducted June 2026.
>
> **What this is not.** Not part of the canonical model, principles, or onboarding flow; nothing here
> is authoritative. It layers on scans 1 and 2 and assumes the reader has them. Dispositions use scan
> 1's structural vocabulary (adopt-instead / incorporate-aspect / cross-reference / complementary /
> tangential) for structural matters and scan 2's content vocabulary (confirm-existing / sharpen-
> existing / adopt-new / adjacent / noted) for principle content.

## 1. Executive summary

The five sources mostly confirm and sharpen scan 2 rather than overturn it, and both refinement
candidates move off "pending." The strongest single finding comes from Lean: it treats **poka-yoke**
(prevent the error before it happens — a gate *before* action) and **andon/jidoka** (stop the moment a
defect is detected — don't pass it *downstream*) as two distinct mechanisms, which maps almost exactly
onto the State Preconditions / Disclosure split and clarifies that the irreversibility gate is "poka-
yoke scaled to cost." **Postcondition-symmetry strengthens to a confirmed recommendation**: a peer
practitioner independently bakes per-step verification gates into plan structure, DbC/PDCA/CRM each
name an exit gate, and Deming adds that it should be built in per-step rather than inspected at the
end. **The irreversibility gate leans toward a clause, not a fifth principle**: every tradition that
addresses it implements a *specific gate scaled to the action's cost* rather than a broad standalone
discipline. The peer work (`how-to-plan`) is high-quality, directly in the handoff-discipline space,
and the right disposition is complementary-with-strong-cross-reference: it solves a different problem
(authoring one tactical plan) with a different carrier (an Anthropic Skill), but its verification-gate
and two-key-deletion patterns are worked instances of exactly the two refinements under discussion.
Theory of Constraints is adjacent — a real coordination framework, but optimizing throughput rather
than the correctness/verifiability the principles target.

## 2. Methodology

Scope was named in the dispatch, so this was depth, not search. I read the peer work directly from its
repository — `SKILL.md` in full plus the `anti-patterns.md` and `plan-template.md` references (the
`example-plan.md` and `file-tree-annotations.md` references are worked-example and ASCII-tree mechanics
that don't bear on principle content, so I skimmed rather than dwelt). I read the Agile Manifesto's
twelve principles from the primary source, and read Lean, Deming, and TOC against multiple reputable
sources centered on their primary texts (Womack & Jones; Deming's *Out of the Crisis*; Goldratt's
*The Goal*). Two sources turned out more load-bearing than expected — Lean's poka-yoke/andon
distinction and the peer work's two-key handshake both speak directly to the irreversibility decision.
One turned out less load-bearing than hoped — TOC, as the dispatch anticipated.

## 3. Findings by source (methodological traditions)

The peer work is target 1 but is assessed in its own section (§5) per the peer-evaluation framing. This
section covers the four traditions.

### Agile Manifesto and its twelve principles (Beck et al., 2001; [agilemanifesto.org](https://agilemanifesto.org/principles.html))

**confirm-existing (Hard Evidence) — "working software is the primary measure of progress."** This is
the Agile form of measuring progress by the artifact rather than the claim: a report of doneness isn't
progress; a working artifact is. It corroborates Hard Evidence's "verify reports against artifacts" and
supplies vocabulary for the postcondition discussion (the exit gate measures the artifact, not the
assertion).

**adjacent — "simplicity: the art of maximizing the amount of work not done."** Aligns with the PAG's
WHAT/WHY-not-HOW minimalism and the killer-items instinct, but it's a design value rather than a
coordination discipline; noted as backing, not a principle.

**adjacent / informative tension — "individuals and interactions over processes and tools," face-to-
face over documentation.** The Manifesto is explicitly *anti*-formalization, which looks like it cuts
against the model's written precondition blocks and contracts. The tension resolves usefully: Agile's
lightweight-handoff stance is premised on co-located humans with continuous conversation and shared
tacit context — exactly the conditions that are *absent* between stateless agents that don't share a
view. The reason the model formalizes where Agile would not is that the agent context lacks the shared
ground Agile assumes. This doesn't yield a principle, but it directly informs the postcondition
decision (see §4): formality is warranted *because* the cheap-coordination preconditions Agile relies
on don't hold for agents.

**Closing assessment.** No adopt-new. One confirm for Hard Evidence; the rest is at a different
altitude (values, not handoff disciplines). The anti-formalization tension is the useful takeaway, and
it argues *for* the model's formality in the agent setting rather than against it.

### Lean Thinking (Womack & Jones, 1996/2003; the five principles, and the Toyota toolkit)

**confirm-existing (Disclosure) and the headline distinction — poka-yoke vs. andon.** Lean draws a
sharp line between two error mechanisms. *Poka-yoke* (mistake-proofing) acts *before* the error — it
makes the error impossible or alerts in time to prevent it; it is preventive and sits at the entry to
an action. *Andon/jidoka* acts *at the moment a defect is detected* — it stops the line so the defect
isn't passed downstream; it is detective and sits at the point of failure. Scan 2 already grounded
Disclosure Is Not Correction in andon/jidoka. Lean's framing now also grounds the *entry* side: State
Preconditions is the poka-yoke of agent handoffs (gate before proceeding), and the irreversibility-gate
candidate is poka-yoke *scaled to the cost of the defect* — Lean explicitly varies poka-yoke device
strength to the severity of the error it guards against. That these are two named, deliberately
separate tools in the most mature tradition is the single most decision-relevant fact in this scan
(see §4).

**confirm-existing (Documentation-First) — "perfection" / kaizen.** The fifth Lean principle, the
pursuit of perfection through continuous improvement, is the same improvement loop as Documentation-
First's lessons-learned capture. Reinforces, doesn't extend.

**adjacent — "flow."** Lean's flow principle (work moves without batching or waiting) is about
throughput across handoffs; it's a real coordination concern but an efficiency one, not a correctness
discipline. Noted, not a gap.

**Closing assessment.** No adopt-new, but the highest-value source in this scan for the *decision*: the
poka-yoke/andon split gives the maintainer a precise, battle-tested way to think about whether the
irreversibility gate is a strengthening of the precondition (poka-yoke) mechanism or a separate
discipline.

### Total Quality Management — Deming's 14 Points and PDCA (*Out of the Crisis*, 1982; [deming.org](https://deming.org/explore/fourteen-points/))

**sharpen-existing (postcondition decision) — PDCA's Check/Study phase.** Plan-Do-Check-Act names an
explicit verification phase after the doing: Check (which Deming later renamed Study, because "check"
implied shallow inspection) is structurally the exit gate. It maps onto a handoff cycle — plan the
prompt, the engineer does the work, *check* the result against the plan, act on the gap — and adds
another vocabulary option for the exit gate alongside DbC's "postcondition" and CRM's "read-back."

**sharpen-existing (Hard Evidence / postcondition implementation) — Point 3, "cease dependence on
inspection."** Deming's point looks at first like an argument *against* verification gates: build
quality in, because inspection is too late: the quality is already in the product. Read correctly it's an argument about *where* verification goes: not as a single terminal
inspection bolted on at the end, but built into each step of the process. This sharpens the
postcondition recommendation — an exit gate should be per-step (the way the peer work's per-commit
Gates are), not one final inspection — and it names an apparent tension with Hard Evidence that
resolves cleanly: Hard Evidence isn't terminal inspection either; verifying by direct observation at
each step *is* building quality in.

**confirm-existing (halt-and-surface) — Point 8, "drive out fear."** Deming wants people to surface
problems rather than hide them. The model's halt-and-surface stance and Disclosure's refusal to bury a
bad commit share that goal. As scan 2 noted for blameless postmortems, the human-psychology mechanism
(fear) doesn't transfer to agents, but the structural goal does: make stopping-and-surfacing the
expected, low-friction action. Confirms the maintainer's reading that the model aligns — with the same
take-the-mechanism-leave-the-therapy caveat.

**Closing assessment.** No adopt-new. Two genuine sharpenings of the postcondition decision (PDCA names
the gate; Point 3 says build it in per-step), and a clean confirm that the model's surface-don't-hide
stance is well-grounded.

### Theory of Constraints (Goldratt, *The Goal*, 1984; the five focusing steps)

**adjacent — the five focusing steps (identify / exploit / subordinate / elevate / repeat).** TOC is a
genuine system-level coordination discipline, and multi-agent planning does have constraint-like
dynamics (planning-agent authoring throughput, engineer pickup latency, the verification gate as a
potential bottleneck). But TOC optimizes *throughput* — it's about doing the work faster by focusing
effort on the bottleneck — whereas the four principles are about doing the work *correctly and
verifiably*. These are orthogonal objectives. A TOC-derived "bottleneck-awareness" principle would
import an efficiency goal the model has deliberately not taken on, and there's no correctness-discipline
gap for it to fill.

**Closing assessment.** No adopt-new; honestly adjacent, as the dispatch anticipated. Interesting lens
for a future *performance* layer if the model ever takes one on, but not a discipline the model is
currently missing. Its "repeat / don't let inertia set in" step is one more instance of the continuous-
improvement loop that Lean (perfection), Deming (improve constantly), and Agile (retrospect and adjust)
all share — the most universal cross-tradition pattern in this scan, and the part of Documentation-First
that is best-grounded.

## 4. Refinement candidates revisited

### Postcondition-symmetry: strengthen — move from "recommended" to "confirmed, with an implementation note"

The new evidence converges hard. Design by Contract (scan 2) names the exit gate "postcondition"; PDCA
names it "Check/Study"; CRM names it "closed-loop read-back"; Agile says the gate measures "working
software." And the peer work (§5) *implements* it: every commit step carries a Gate that must pass
before the next begins, and the plan closes on a verification checklist. So a fellow practitioner in the
same space independently concluded that handoffs need an explicit, structural exit gate — strong
external validation.

Two specifics for the maintainer if this is adopted:

- **Vocabulary.** "Postcondition" is the cleanest fit, precisely because the entry gate is already
  "precondition" — the symmetry is self-documenting. PDCA's "check" and CRM's "read-back" are good
  second choices but break the matched pair.
- **Implementation (from Deming Point 3).** Make the exit gate *per-step / built-in*, not a single
  terminal inspection. The peer work's per-commit Gate is the right shape; a lone "verify at the end of
  the handoff" gate would be exactly the terminal inspection Deming warns against.

This also cleanly absorbs scan 2's "closed-loop completion" candidate: naming the postcondition gate
*is* closing the loop.

### Irreversibility gate: leans toward a clause (scaled precondition), not a fifth principle

Every tradition that addresses irreversibility implements it as a *specific gate scaled to the cost of
being wrong*, not as a broad standalone discipline:

- The peer work gates one specific irreversible action — plan deletion — behind a developed two-key
  handshake (explicit human confirmation, path verification, a dangling-handshake fallback that refuses
  to delete unilaterally). A practitioner in the exact same space chose a *specific protocol for the
  specific irreversible action*, which is the clause/scoped-gate shape.
- Lean treats the irreversibility gate as poka-yoke scaled to defect cost — a strengthening of the
  preventive entry gate, not a separate mechanism from it.
- Surgery (time-out scaled to procedure), aviation (challenge-and-response scaled to flight phase), and
  the nuclear two-person rule (scaled to launch authority) are all *escalations of verification before a
  high-stakes action*, implemented as action-specific protocols.

The cleanest reading: add a clause to State Preconditions — *irreversibility (or high cost-to-reverse)
escalates the verification bar* — treating it as poka-yoke scaled up, with Disclosure continuing to
cover the detective/stop-at-defect side. Lean's poka-yoke/andon distinction is the reason to keep the
two conceptually crisp even if both live as clauses rather than a new principle: prevention-before and
stop-at-defect are genuinely different mechanisms, and the model already splits them across State
Preconditions and Disclosure.

Honest counter-consideration: if irreversible operations *other than* commits turn out to be common in
practice (history rewrites, force-pushes, destructive external actions via tools), a named principle
might earn its inheritance cost by giving the discipline one place to live rather than scattering
clauses. The evidence leans clause; the frequency of non-commit irreversible actions is the maintainer's
call, and it's a judgment the prior art informs but doesn't settle.

## 5. Friend's work assessment: `simiancraft/simiancraft-skills`, the `how-to-plan` skill

Given the same care as the broader scans, neither soft-pedaled for the personal connection nor inflated
by shared space. Read in full: `SKILL.md`, `references/anti-patterns.md`, `references/plan-template.md`.

**What it is.** A methodology, packaged as an Anthropic Skill, for producing tactical hand-off-ready
planning documents. It opens on the same premise the model centers — that a plan is the contract between the session that designs the work and the session that executes it, and only works if it's explicit enough to hand off cold to a fresh agent. It specifies an
interrogation protocol (one question at a time, propose the recommended answer, walk the decision tree,
read the codebase rather than ask, don't stop early), a five-value scope model
(model/subsystem/cross-stack/project-meta/cross-repo) that governs what a plan may reference and where
it lives, required front matter and sections, ASCII before/after file trees, atomic commit steps each
with a verification Gate, and an "Inspector Gadget Rule" under which plans self-destruct (are deleted,
not archived) when done, behind a two-key handshake.

**Structural disposition: complementary, with strong cross-reference.** It solves a *different problem*
at a *different layer*: how to author one tactical plan document, where multi-agent-planning is a
governance-and-inheritance model for coordination principles across projects. Its carrier is also
different — an Anthropic Skill (SKILL.md plus references, invoked by a trigger description), which scan
1 classed among the copy/install discipline-carriers that lack provenance and origin/mode tracking.
`how-to-plan` is a single skill, not an inheritance system, so it doesn't engage the cross-tenant
problem at all. That said, two structural rhymes are worth a cross-reference: its scope model has a
genericity gradient that echoes the model's own (its `cross-repo` scope uses generic placeholders and
takes "the same depth gate as a global skill" — the reference-mode/canonical-genericity instinct), and
its tactical-vs-durable artifact split (plans self-destruct; "convention docs, rule files, skill docs"
are durable) mirrors the model's split between disposable prompts/logs and durable principles/PAG,
including the "extract any keeper convention before deleting" step.

**Content dispositions.**

- **confirm-existing (State Preconditions).** The contract-between-designing-and-executing-session
  framing is the same wisdom as State Preconditions, arrived at independently — more validation that the
  handoff-as-contract model is sound.
- **sharpen-existing (postcondition-symmetry) — the most useful content finding.** Per-commit Gates
  plus a closing verification checklist are a worked, operational instance of the exit-gate refinement
  §4 recommends. Where multi-agent-planning currently states verification as prose principle,
  `how-to-plan` bakes it into the plan's structure step by step. At this specific point it is *more
  concrete* than the model, and that concreteness is the thing worth borrowing.
- **incorporate-aspect (irreversibility gate).** The two-key handshake before plan deletion is a
  developed irreversibility gate: it proposes only when verification passes, requires an explicit
  confirmation phrase (ambiguous assent doesn't count), verifies the path, and refuses to delete
  unilaterally when the human is absent. It's the clearest peer instance of the irreversibility
  discipline §4 discusses, and it supports the clause/scoped-gate reading (a specific protocol for a
  specific irreversible action).
- **confirm-existing (reference-mode / convention discipline).** Its anti-patterns "don't restate
  workflow conventions — assume them; mention only deviations" and "don't paraphrase the convention;
  link it" are the same instincts as the model's reference-mode (link canonical, don't copy) and its
  subliminal-norm principle (behavior from structure, not restated rules).

**Is any of it better than the model at a specific point?** Yes, narrowly and honestly: at
operationalizing the verification gate, `how-to-plan` is ahead — it makes the gate a required structural
element of every plan rather than a principle the author must remember to honor. That's not adopt-
instead (different problem, different layer), but it is a concrete pattern the model could learn from
when it acts on the postcondition refinement.

**Closing assessment.** Well-aligned peer work, solving an adjacent and complementary problem, worth a
genuine cross-reference. It independently confirms State Preconditions and provides the best worked
instances of *both* refinement candidates. The honest disposition is complementary-with-cross-reference
on structure and confirm/sharpen on content — credited plainly, because it earns it.

## 6. Recommendations

Concrete suggestions for the maintainer, in rough priority order. None are changes I have made; they are
proposals to weigh, and the decisions remain the maintainer's.

Adopt the postcondition-symmetry refinement, and implement it per-step. The evidence is now strong and
convergent: name an explicit postcondition/exit gate for completion (vocabulary: "postcondition," for
symmetry with "precondition"), and make it per-step/built-in rather than a single terminal inspection,
per Deming's Point 3 and the peer work's per-commit Gates. This subsumes scan 2's closed-loop-completion
candidate.

Resolve the irreversibility gate as a clause, pending a frequency check. The prior art leans toward a
scaled-precondition clause ("irreversibility escalates the verification bar") rather than a fifth
principle, because every tradition implements it as an action-specific scaled gate. Keep it conceptually
distinct from Disclosure along Lean's poka-yoke (prevent-before) vs. andon (stop-at-defect) line. Revisit
the principle-vs-clause question only if non-commit irreversible actions prove common enough to warrant a
single home.

Cross-reference the peer work. A reference to `simiancraft/simiancraft-skills`' `how-to-plan` — as
complementary work that independently confirms the handoff-as-contract model and demonstrates per-step
verification gates and an irreversibility handshake — would credit a fellow practitioner and give
adopters a concrete, Skill-packaged companion at the tactical-plan-authoring layer the model itself
doesn't cover.

Use the cross-tradition lineage if establishing credibility. Layered on scan 2's lineage note, the
follow-up adds: State Preconditions ≈ poka-yoke (entry gate) and Disclosure ≈ andon/jidoka (defect stop)
as Lean's two distinct mechanisms; the postcondition gate ≈ PDCA's Check/Study; "build it in per-step"
≈ Deming's Point 3. The convergence of manufacturing, quality management, aviation, surgery, and a peer
practitioner on these same shapes is the credibility argument.

Leave Theory of Constraints out. Adjacent and interesting, but it optimizes throughput, not the
correctness/verifiability the principles target; adopting from it would import a different objective for
no coverage gain. Reported for the record.

---

*Focused follow-up scan compiled June 2026. Primary sources cited inline; findings layer on
`docs/related-work.md` and `docs/principles-prior-art.md` and are a snapshot, not a maintained index.*
