# Principles prior-art scan

> **What this is.** A content-prior-art scan for the four canonical principles in this repository,
> plus a survey for candidate principles the model may be missing. It asks two questions: is the
> *wisdom* in each principle well-grounded against other people's writing, and are there load-bearing
> disciplines from adjacent fields the model hasn't captured? Conducted June 2026.
>
> **What this is not.** It is *not* part of the canonical model, the principles, or the onboarding
> flow, and nothing here is authoritative or normative. It is research: a reading list with analysis.
> The companion [`docs/related-work.md`](./related-work.md) covers prior art for the *structural* model
> (the inheritance architecture); this document covers prior art for the *content* (the principles
> themselves).

## 1. Executive summary

The four principles are well-grounded. Each is, in effect, an AI-coordination translation of a
discipline that mature high-reliability fields converged on long ago — and the convergence across
unrelated domains (aviation, surgery, manufacturing, security, scientific method, software
engineering) is itself strong evidence the disciplines are real rather than idiosyncratic. The single
strongest finding: **State Preconditions is Design by Contract applied to agent handoffs** — a near-
exact structural mapping to Bertrand Meyer's caller/callee obligation model, which both validates the
principle and offers a refinement (Meyer's contracts are symmetric — preconditions *and*
postconditions — while the principle currently specifies only the entry gate). On the missing-
principles question, the honest result is that the four cover their ground well; the one candidate
that clears a high bar is an explicit **pause-before-irreversible-action** discipline, and even that
overlaps the existing four enough that it may be better as a refinement than a fifth principle. No
strong load-bearing gap went unnamed. A few optional sharpenings are noted per principle; none is
required for the principles to stand.

## 2. Methodology and search scope

I read all four principle files and `docs/model.md` in full before searching, and skimmed
`docs/related-work.md` for context. I then searched each principle's underlying wisdom against primary
sources in the fields the dispatch named: software engineering (design by contract, defensive
programming, debugging discipline, SRE), scientific method (Feynman), aviation crew resource
management, surgical safety, manufacturing quality (the Toyota Production System), security (zero
trust, least privilege), organizational behavior (normalization of deviance), and published AI agent
coordination guidance (Anthropic's agent and context-engineering essays). I favored primary or near-
primary sources: Meyer's own writing on DbC, Feynman's 1974 Caltech address, the Google SRE book,
Gawande's account of the WHO checklist, Vaughan's Challenger analysis, Agans' debugging rules, and
Anthropic's engineering essays.

There is a real asymmetry in the literature worth stating up front. The disciplines the principles
encode are *mature* in their home fields — aviation CRM, surgical checklists, and DbC each have
decades of literature and measured outcomes. The translation of those disciplines *to AI agent
coordination specifically* is sparse and recent; almost no one has written the mapping down. So the
prior art is strong on the wisdom and thin on the application, which is consistent with the structural
scan's finding that the AI-coordination-discipline space is underexplored. I did not pursue runtime
agent frameworks, generic prompt-engineering tutorials, or pure code-quality writing (DRY/SOLID)
except where a clear coordination-discipline dimension was present.

## 3. Findings for each existing principle

### Hard Evidence

**confirm-existing — Feynman, "Cargo Cult Science" (Caltech commencement address, 1974;
[calteches.library.caltech.edu](https://calteches.library.caltech.edu/51/2/CargoCult.htm)).** Feynman's
formulation of scientific integrity — you must not fool yourself, and you are the easiest person to fool — is the
same wisdom Hard Evidence encodes: assumptions are suspect, and the person most likely to be deceived
by an unverified belief is the one holding it. Feynman's discipline of reporting everything that might
invalidate your own result maps directly onto the principle's "surprising findings" and "verify even
when uncontradicted" clauses. This is the deepest validation in the set.

**confirm-existing — zero trust / "never trust, always verify" (NIST SP 800-207; widely documented,
e.g. [crowdstrike.com](https://www.crowdstrike.com/en-us/cybersecurity-101/zero-trust-security/)).**
Zero trust removes implicit trust and requires *continuous* verification rather than one-time
validation — the security-architecture form of "state drifts; verify at the moment of use" and "it
worked last time is not evidence." The principle's instruction to treat engineer reports and tool
outputs as untrusted until directly observed is zero trust applied to inter-agent communication.

**confirm-existing — Agans, *Debugging* Rule 3, "Quit Thinking and Look" (Dave Agans, 2002;
[publisher listing](https://books.google.com/books/about/Debugging.html?id=jynA9ECbBsgC)).** Agans'
rule — examine the evidence before hypothesizing, get data first rather than guessing — is Hard
Evidence in a debugging idiom, with the sharp observation that practitioners *prefer* thinking to
looking because looking feels like more work. That maps to the principle's "cost discipline" section.

**adjacent — surgical and aviation two-person verification (CRM challenge-and-response; surgical
sponge/instrument counts).** The "flaps identified / flaps verified" callout and the surgical count
are independent-second-observer checks of a state-changing action. They corroborate the principle's
"agent reports" clause (the reviewer confirms by reading the artifact, not by trusting the claim) but
operate at the level of a specific verification *technique* rather than the general principle.

**Closing assessment: Well-grounded; minor refinement candidate.** The one thing the prior art names
that the principle states only implicitly is Feynman's *self-directed* dimension — the discipline is
not just "verify external state" but "actively try to disprove your own conclusion." The principle's
"surprising findings" clause gestures at this; a sentence making the self-skepticism explicit would
tighten it.

### Documentation-First

**confirm-existing — Google SRE runbooks/playbooks and blameless postmortems (*Site Reliability
Engineering*, O'Reilly, 2016; [sre.google/sre-book/postmortem-culture](https://sre.google/sre-book/postmortem-culture/)).**
SRE's playbook discipline (consult the established procedure before improvising) and its postmortem-to-
runbook loop (capture each incident so it doesn't, in the book's framing, recur indefinitely) are the
operational-knowledge-management form of Documentation-First's core claim: projects capture knowledge
so future agents don't rediscover obstacles. The principle's "lessons-learned files" are precisely the
postmortem artifact. SRE adds a caveat the principle could absorb: a runbook only helps if responders
are *aware* it exists and it's kept current — discoverability and staleness are first-order concerns.

**confirm-existing — Agans, *Debugging* Rule 1, "Understand the System / Read the Manual."** The first
move when debugging is to read the documentation and understand the tools, not to start changing
things — the same "read before iterating" discipline. Notably, Agans pairs Rule 1 (read) with Rule 3
(look) the same way the principle composes Documentation-First with Hard Evidence: read what's known,
then verify by observation.

**adjacent — Gawande, checklists as institutionalized knowledge (*The Checklist Manifesto*, 2009).**
A checklist is captured operational knowledge that frees the practitioner from re-deriving steps under
load. This supports the spirit of Documentation-First but is closer to a tool than to the "consult
before iterating" reflex the principle targets.

**Closing assessment: Well-grounded; minor refinement candidate.** This was the principle I expected
to be least-grounded, and it held up — though its prior art is about knowledge *management* rather than
a single named principle. The available sharpening is SRE's point that documentation must be current
and discoverable to be load-bearing; the principle assumes the docs are findable and fresh.

### State Preconditions

**confirm-existing (headline) — Bertrand Meyer, Design by Contract (Eiffel; *Object-Oriented Software
Construction*, 1988/1997; [se.inf.ethz.ch Meyer chapter](https://se.inf.ethz.ch/~meyer/publications/old/dbc_chapter.pdf)).**
This is the strongest single mapping in the scan. DbC frames a call as a contract: the caller is
obligated to satisfy the precondition before calling; the callee may assume it. A precondition
violation is raised *at the call site*, identifying who broke the contract — exactly the principle's
"engineer verifies first; any mismatch is halt-and-surface, attributed to the boundary." The planning
agent authoring a prompt is the caller stating obligations; the engineer is the callee verifying them.
DbC even shares the principle's stance against defensive sprawl: a contract declares what must be true
rather than checking everything everywhere, which is why the principle specifies distinctive state
fragments rather than full-content validation.

**sharpen-existing — DbC's symmetry (preconditions *and* postconditions).** Meyer's contracts are two-
sided: the precondition is the caller's obligation, the postcondition is the callee's guarantee on
return. State Preconditions specifies only the entry gate. The exit side — what the engineer guarantees
true on completion, which the planning agent then verifies — is currently handled informally under Hard
Evidence ("verify reports against artifacts"). Adopting DbC's symmetry would let the model name an
explicit *postcondition block* for completion, closing the handoff loop at both ends rather than one.

**confirm-existing — surgical "time-out" and the WHO Surgical Safety Checklist (Gawande / WHO, 2008–09;
[atulgawande.com](https://atulgawande.com/book/the-checklist-manifesto/)).** The pre-incision time-out
is a structured pause to verify state — right patient, right site, right equipment — immediately before
an irreversible action. That is State Preconditions in the operating room: confirm the world matches
expectations before proceeding. Gawande's design lessons are directly transferable: keep the gate short
(the WHO checklist runs under two minutes), include only the "killer items," and use a Do-Confirm
structure (act, then pause to confirm nothing was missed). The principle's "distinctive fragments, not
full content reproductions" already follows the killer-items instinct.

**confirm-existing — aviation handoff and closed-loop communication (CRM; closed-loop read-back).**
Crew resource management requires receivers to read critical instructions back to confirm accuracy,
and treats read-back/hear-back failures as a named error class. The principle's precondition block is
the handoff equivalent: a structured confirmation that the receiver's view matches the sender's before
work proceeds. CRM also grounds the principle's halt-and-surface stance through its assertive-
communication norm — the lower-status party (first officer; here, the engineer) is *expected* to
surface a discrepancy rather than defer to the senior party's described state.

**Closing assessment: Well-grounded; refinement candidate: postcondition symmetry.** The principle is
the best-grounded of the four. The one substantive refinement is DbC's two-sidedness: name the exit
gate (postcondition) explicitly rather than leaving completion verification to Hard Evidence.

### Disclosure Is Not Correction

**confirm-existing — the Toyota Production System's jidoka and the andon cord (TPS; widely documented,
e.g. [Toyota UK](https://mag.toyota.co.uk/toyota-manufacturing-25-objects-andon-cord/),
[Art of Lean](https://artoflean.com/reference/jidoka/)).** Jidoka builds quality in by stopping the
line the moment a defect is detected, rather than passing it downstream where it gets built into the
finished product. That is exactly the principle's rule: do not commit known-bad state to `main` (do not
pass the defect downstream); stop, make the problem visible, and resolve the root cause before
proceeding. The principle's three permitted responses — test the assumed constraint, hand off, write a
cleanup prompt — parallel jidoka's halt-investigate-fix sequence. The andon empowerment norm ("two
ways to get in trouble: don't show up, or don't pull the cord when you see a problem") reinforces the
principle's stop-don't-ship stance.

**confirm-existing — normalization of deviance (Diane Vaughan, *The Challenger Launch Decision*, 1996;
[en.wikipedia.org](https://en.wikipedia.org/wiki/Normalization_of_deviance)).** This is the precise
mechanism behind the principle's most distinctive claim — that committing-with-disclosure is contagious
across agents. Vaughan's finding: when a deviation from standard produces no immediate catastrophe, the
absence of bad feedback gradually reclassifies the deviation from "risky exception" to "normal
practice." The principle's contagion paragraph (a downstream agent reads the placeholder, infers the
discipline is acceptable, and ships its own degraded commit on top) *is* normalization of deviance
operating at the seam between agents. Naming the mechanism explicitly would strengthen the section.

**sharpen-existing — naming the mechanism.** Both findings above point the same way: the principle
describes the failure shape vividly but doesn't name the two established mechanisms (defect-flows-
downstream from jidoka; deviation-becomes-norm from Vaughan). Citing them would give the principle's
"failure shape" and "contagion" sections a recognized vocabulary and external backing.

**Closing assessment: Well-grounded; minor refinement candidate.** The wisdom is solid and
independently arrived at in both manufacturing quality and accident sociology. The optional sharpening
is vocabulary, not substance.

## 4. Candidate new principles

The dispatch sets a deliberately high bar for **adopt-new**: a candidate must be load-bearing,
uncovered by the existing four, and demonstrably worth articulating in other writers' practice. Held to
that bar, the honest result is that only one candidate is strong, and it overlaps the existing four
enough to be arguable as a refinement. The rest are weaker and reported as such.

**Candidate (strongest): Pause Before Irreversible Action.** *Before an operation that is irreversible
or expensive to reverse — a push to `main`, a history rewrite, a force-push, a destructive overwrite —
an agent applies a verification gate proportional to the cost of being wrong, and escalates to
independent confirmation or hand-off rather than proceeding on a single agent's judgment.* The prior
art is unusually broad and convergent: Anthropic's own agent guidance recommends building checkpoints
where an agent pauses before irreversible actions like deleting data
([anthropic.com/engineering/building-effective-agents](https://www.anthropic.com/engineering/building-effective-agents));
the surgical time-out gates the irreversible incision; aviation challenge-and-response gates gear/flap
changes; the nuclear two-person rule gates irreversible launch actions. The case for inclusion: the
*trigger* here is irreversibility itself, and that trigger is not named in the current four. Disclosure
Is Not Correction covers known-bad commits and State Preconditions covers state mismatch, but neither
says "the more irreversible the action, the higher the verification bar." Honest caveat: because the
two existing principles already cover the most common irreversible action (a bad commit to `main`),
this may land better as a *clause added to* State Preconditions and Disclosure — "irreversibility
escalates the gate" — than as a standalone fifth principle. I'd present it to the maintainer as
adopt-new-or-sharpen, leaning toward letting the maintainer choose based on how often irreversible
operations *other than* commits arise in practice.

**Candidate (moderate): Closed-Loop Completion.** *A handoff is not complete when the work is done; it
is complete when completion has been verified against the artifact and confirmed back to the
originator.* Prior art: DbC postconditions (the callee's guarantee on return) and CRM closed-loop read-
back (the loop isn't closed until the receiver confirms). Honest assessment: this is weaker as a *new*
principle because it is already substantially present — Hard Evidence requires verifying reports
against artifacts, and the read-back-after-commit practice is the completion gate in operator use. The
gap is only that the *symmetric exit gate* isn't named as its own discipline. This is better treated as
the postcondition-symmetry refinement to State Preconditions (above) than as a separate principle.

**Candidate (weak): Role-Boundary / Least Privilege.** *An agent operates only within the authority its
role grants and treats scope expansion as a halt-and-surface, not a judgment call.* Prior art: the
security principle of least privilege; aviation's sterile-cockpit focus discipline. Honest assessment:
this is mostly already encoded in the model — structurally, in the planning/engineer governance
boundary (engineers don't edit planning guidelines; planning defines WHAT/WHY, not HOW). Promoting it
to a principle would restate an architectural rule the model already enforces. Reported as **noted**,
not adopt-new. (Worth flagging: the sterile-cockpit analogy carries its own documented failure mode —
crew members suppressing genuinely necessary signals because they over-applied the focus rule — which
is a reason to be cautious about a hard scope-silence discipline for agents.)

**Candidate (weak): Halt-and-Surface as Default Under Uncertainty.** A cross-cutting "when in doubt,
stop and surface rather than proceed on a guess." Prior art: jidoka's andon, fail-safe defaults, CRM
speak-up. Honest assessment: this is already distributed across State Preconditions (mismatch = halt)
and Disclosure (stop, don't ship). Naming it as a meta-principle risks redundancy. **Noted.**

## 5. Patterns observed

The principles are translations, and that's a strength. Each of the four maps to a discipline that an
unrelated high-reliability field arrived at independently — Hard Evidence to scientific empiricism and
zero trust, Documentation-First to SRE runbooks, State Preconditions to Design by Contract and surgical
time-outs, Disclosure Is Not Correction to jidoka and normalization of deviance. When manufacturing,
surgery, aviation, security, and physics independently converge on the same discipline, the discipline
is not a local preference; it's a property of coordinating fallible actors around high-stakes shared
work. The principles inherit that durability.

The richest unborrowed vein is aviation/surgical *handoff* discipline. Both fields have decades of
specific, tested practice on the exact problem the model centers — transferring responsibility for
shared state between fallible actors without dropping context. Closed-loop read-back, challenge-and-
response, the time-out, the sign-out, "who is empowered to call the stop" — these are more developed
than anything in the AI-coordination literature, and the model has so far drawn on the general shape
(precondition block) more than the specific techniques (symmetric read-back, designated stop-caller).

There's a human-versus-agent asymmetry worth keeping in view. Several of the source disciplines are
wrapped in *cultural* machinery aimed at human psychology — blameless postmortems exist to overcome
humans' fear of punishment; CRM's assertive-communication training exists to overcome cockpit
hierarchy. For AI agents the transferable core is the *artifact and the protocol* (capture the lesson;
require the read-back), not the emotional-safety scaffolding. When borrowing from these fields, the
model should take the mechanism and leave the therapy — a distinction the existing principles already
get right by encoding behavior in workflow structure rather than in exhortation.

Finally, the prompt-authoring philosophy in the PAG has strong external backing that sits just outside
the four principles. Anthropic's context-engineering guidance — find the smallest set of high-signal
tokens; aim for the "right altitude" between brittle over-specification and vague under-specification
([anthropic.com/engineering/effective-context-engineering-for-ai-agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents))
— is essentially the WHAT/WHY-not-HOW discipline stated in context-window terms. Not a principle gap,
but a useful citation if the PAG ever wants external grounding.

## 6. Recommendations

Concrete suggestions for the maintainer, in rough priority order. None are changes I have made; they
are proposals to weigh.

Treat the four as confirmed and say so. The most useful output of this scan is the confirmation
itself: the principles are well-grounded across multiple mature traditions. If the model or principles
README ever wants to establish credibility with adopters, a short "intellectual lineage" note —
State Preconditions ≈ Design by Contract, Disclosure ≈ jidoka/stop-the-line, Hard Evidence ≈ scientific
empiricism and zero trust, Documentation-First ≈ SRE runbook discipline — would do real work at low
cost.

Consider the postcondition-symmetry refinement to State Preconditions. This is the highest-value
substantive change surfaced: Design by Contract is two-sided, and naming an explicit completion/
postcondition gate (the engineer's guarantee on return, verified by the planning agent) would close the
handoff loop at both ends. It also absorbs the "Closed-Loop Completion" candidate cleanly.

Decide whether irreversibility deserves its own gate. The pause-before-irreversible-action candidate is
the only genuine content gap, and it has unusually broad prior art including Anthropic's own guidance.
Decide whether it's a fifth principle or a clause added to State Preconditions and Disclosure
("irreversibility escalates the verification bar"). Either resolves the gap; the choice depends on how
often irreversible operations other than commits arise.

Adopt the optional vocabulary sharpenings if revising the relevant files anyway. Name normalization of
deviance in Disclosure's contagion section; name the self-skepticism dimension (Feynman) in Hard
Evidence; note discoverability/staleness (SRE) in Documentation-First. These are citations and one-
sentence additions, not restructurings — worth doing only when those files are open for other reasons.

Leave the weak candidates out. Role-boundary/least-privilege and a standalone halt-and-surface meta-
principle are already encoded structurally; promoting them would add inheritance cost across all tenants
for no new coverage. Reported here for the record, not for adoption.

---

*Content prior-art scan compiled June 2026. Primary sources cited inline; the disciplines surveyed are
mature in their home fields, and the findings are a snapshot of how the principles sit against them, not
a maintained index.*
