# Related work: a landscape scan

> **What this is.** This document is a landscape scan of prior and related work in the space of
> sharing agent-coordination disciplines across projects. It compares the multi-agent planning model
> (see [`docs/model.md`](./model.md) and [`docs/principles/`](./principles/)) against existing work, to
> help adopters and maintainers understand where this project sits relative to predecessors and
> neighbors.
>
> **What this is not.** It is *not* part of the canonical model, the principles, or the onboarding flow.
> Nothing here is authoritative or normative. It is research, conducted June 2026, and reflects the
> state of the surrounding ecosystem at that time. Treat it as a reading list with analysis, not as
> guidance to follow.

## 1. Executive summary

The space divides cleanly into two problems that are easy to conflate: **scoping agent instructions
within a single repository** (well-solved, by many tools) and **reusing coordination disciplines
across many projects and organizations with controlled local adaptation** (largely unsolved in the
AI-tooling world). This project addresses the second. No surveyed work does the second problem better
for *coordination disciplines* specifically; there is no **adopt-instead** candidate. The strongest
structural prior art for the layered publish/adopt/adapt factoring is not in AI tooling at all — it
lives in compliance frameworks (NIST SP 800-53 catalog / baseline / overlay, formalized by OSCAL) and
configuration-management registries (Terraform public/private modules), both of which solved
canonical-publish-then-adapt years ago and offer vocabulary and one or two mechanisms worth borrowing.
The disciplines this project publishes (hard evidence, documentation-first, handoff preconditions,
commit discipline) are being independently rediscovered elsewhere, which validates the content while
underlining that the novel contribution is the inheritance model, not the disciplines. The
inheritance-model axis — explicit origin/mode tracking with provenance, defaulting to live reference
rather than installed copies — is genuinely underexplored in publicly available agent-coordination
work.

## 2. Methodology and search scope

I read the repository first — `README.md`, `docs/model.md`, the `docs/principles/` index and the
`hard_evidence` principle in full — to ground the comparison in the actual artifact. I then searched
across five areas the model touches: (a) agent-instruction file conventions and their composition
rules (`AGENTS.md`, `CLAUDE.md`, `.cursor/rules`); (b) shared/reusable rule libraries and the
copy-versus-reference question; (c) configuration-management registries with public/private/org tiers
(Terraform); (d) standards-body catalog/baseline/overlay/profiling models (NIST 800-53, OSCAL); and
(e) multi-agent coordination disciplines published as standalone artifacts rather than runtime
frameworks. I favored primary sources — official documentation, standards publications, project
repositories, the `agents.md` site — over secondhand summaries.

I deliberately did not evaluate AI agent *runtime* frameworks (AutoGen, LangGraph, CrewAI, MetaGPT) on
their orchestration mechanics: this model is explicitly not a runtime, and comparing it to one would
be a category error. I note one runtime concept (MetaGPT's standardized operating procedures) only
because it is the nearest discipline-flavored idea in that space. I also set aside generic prompt-
engineering tutorials, product walkthroughs, and AI-safety theory without coordination artifacts
attached.

## 3. Findings

### adopt-instead

None. I went looking for something that does cross-organization reuse of *coordination disciplines*
with controlled local adaptation, and did better than the three-layer model. I did not find it. The
closest candidates either solve a different problem (intra-repo instruction scoping), carry different
content (tech-stack conventions rather than coordination disciplines), or use a weaker reuse mechanism
(copy/install without provenance). This is a real result, not a face-saving one: the specific
combination this project assembles does not appear to exist elsewhere in publicly available form.

### incorporate-aspect

**NIST SP 800-53 control catalog + baselines + overlays, formalized in OSCAL.**
[csrc.nist.gov/projects/risk-management](https://csrc.nist.gov/projects/risk-management/sp800-53-controls/overlay-repository/overlay-overview)
and the OSCAL profile model ([pages.nist.gov/OSCAL](https://pages.nist.gov/OSCAL/learn/concepts/layer/control)),
maintained by NIST, current through 800-53 Rev 5 and actively developed in 2026. This is the closest
structural match to the model anywhere, despite being from compliance rather than AI. A *catalog* of
controls is the authoritative statement of each (the canonical principles); a *baseline* is a curated
selection of controls (the operator registry's selection of which principles tenants inherit); an
*overlay* is a community-of-interest or context-specific customization of a baseline (the operator's
adapted-upstream and self-originated principles); *tailoring* is the adaptation operation. Crucially,
the OSCAL profile model lets a profile import from a catalog **or from another profile that has
already been defined** — exactly the recursive structure where an operator umbrella is itself a tenant
of public. And OSCAL distinguishes resolving a profile fresh against its source from baking it into a
standalone *resolved catalog* — which maps almost exactly onto this model's reference mode (resolve
fresh, drift normal) versus adapted mode (hold a local resolved copy with a pin). The aspect worth
incorporating is the **vocabulary and the explicit separation of selection from tailoring**: OSCAL has
spent years making "what you import" distinct from "how you modify it," which is the same distinction
this model draws as Origin versus Mode. Borrowing the catalog/profile/import/resolve framing — or at
minimum acknowledging it — would sharpen the model and signal that the factoring rests on well-tested
prior art rather than being improvised. The honest difference: OSCAL is heavyweight, machine-readable
XML/JSON with tooling and a compliance mandate; this model is intentionally lightweight prose in
markdown. The factoring is shared; the weight is not.

**Terraform module versioning discipline.**
HashiCorp's public and private module registries
([developer.hashicorp.com](https://developer.hashicorp.com/terraform/cloud-docs/registry/publish-modules)),
current and widely used. Consumers reference modules by semantic version (`version = "~> 1.2"`), and
authors signal breaking versus non-breaking change through the version number. This model currently
pins adapted-mode consumers to a canonical SHA, which records *which* version a local adaptation
reflects but says nothing about whether a later canonical change is cosmetic or substantive. The
aspect worth considering is whether canonical principles should additionally carry a **semantic
version**, so an adapted-mode consumer can tell at a glance whether a canonical update is a wording
tweak (no re-translation needed) or a substantive revision (re-translation needed). A SHA answers
"which version"; a semver answers "do I need to act." The model already treats re-translation as the
cost of adapted mode; semver would make the re-translation trigger legible.

### cross-reference

**multi-agent-coordination-framework** (timothyjrainwater-lab),
[github.com/timothyjrainwater-lab/multi-agent-coordination-framework](https://github.com/timothyjrainwater-lab/multi-agent-coordination-framework),
updated early 2026. This is the closest *content* peer found: a methodology that publishes coordination
disciplines — not a runtime — discovered bottom-up by an operator coordinating Claude and GPT agents on
a real codebase. Its headline insight, that agent-written prose drifts from reality within a few
handoffs while machine-generated state does not, so build scripts should produce canonical facts and
everything else is commentary, is the same family as this project's Hard Evidence principle and its
treatment of committed git state as coordination truth. Its emphasis on protocols over shared state —
handoff checklists, consistency gates, session-scope declarations — overlaps with State Preconditions
and Documentation First. Where it differs structurally: it is a single-project pattern catalog with
copy-in templates and no reuse-across-projects model — no canonical source, no inheritance, no origin
or mode tracking. It does the disciplines; it does not do the inheritance. That makes it an ideal
cross-reference: the independent rediscovery of the same disciplines is evidence the disciplines are
real, and the absence of any reuse model in it highlights precisely what this project adds.

**AGENTS.md** ([agents.md](https://agents.md/)), an open format now stewarded by the Agentic AI
Foundation under the Linux Foundation, used by 60k+ projects, with `CLAUDE.md`, `.cursor/rules`, and
similar as siblings. This is the dominant agent-instruction convention and almost every adopter of
this model will already have one. Its composition model is a **proximity cascade**: the agent reads the
nearest file in the directory tree and the closest one wins. That is implicit, position-based override
*within a single repository*. This model is the opposite shape: explicit, registry-declared inheritance
*across repositories and organizations*, with named origin and mode. The two are complementary rather
than competing — AGENTS.md governs how instructions scope inside one repo; this model governs how
coordination principles propagate across many. A reference from the README clarifying that relationship
would help adopters place the model correctly (it sits above their AGENTS.md/CLAUDE.md, not in place of
it). Worth noting: cross-*tool* propagation (one instruction set across Cursor, Codex, Claude Code) is
handled by tools like `ruler` and `rule-porter`, which is a third, orthogonal axis — neither intra-repo
scoping nor cross-org reuse, but format translation.

### complementary

**Claude Code plugins and the plugin/skill marketplace model**
([code.claude.com/docs](https://code.claude.com/docs/en/plugin-marketplaces), org-level marketplaces
documented at [support.claude.com](https://support.claude.com/en/articles/13837433-manage-claude-cowork-plugins-for-your-organization)),
shipped late 2025 and expanding through 2026. This is the same propagation problem solved with a
different mechanism. It has emerging layers that rhyme with this model — an official curated
marketplace (akin to public canonical), private team/org marketplaces an admin curates for everyone
(akin to an operator umbrella), and project-scoped versus user-scoped installation (akin to tenant
versus operator reach). But the mechanism is **packaging and install**: a plugin or skill is copied
into place, there are no automatic updates (reinstalling pulls the latest), and there is no notion of
an org *adapting* an official plugin while recording provenance back to it — you install it or you fork
it and lose the link. So it lacks both reference mode (live resolution, drift-as-normal) and the
adapted-upstream origin with a provenance marker. It is complementary in two ways: it is the substrate
many adopters already run on, and it is a plausible *distribution channel* for these principles (one
could ship the principles as a skill) while the git-native inheritance model remains the system of
record for origin and mode.

**Community rule catalogs** — `awesome-cursorrules`
([github.com/PatrickJS/awesome-cursorrules](https://github.com/PatrickJS/awesome-cursorrules)),
`cursor.directory`, and company starter sets like `maddevsio/shared_cursor_rules`
([github.com/maddevsio/shared_cursor_rules](https://github.com/maddevsio/shared_cursor_rules)). These
demonstrate real demand for shareable agent guidance, and the maddevsio set in particular tiers rules
into `generic/must-have` (mandatory across all company projects, adapt but preserve the essence),
`generic/optional`, and project-specific — which is a lightweight version of the operator layer's
curate-and-adapt idea, including the notion that some inherited rules are non-waivable. They are
complementary rather than prior art for two reasons: the content is overwhelmingly tech-stack
conventions (framework usage, code style) rather than coordination disciplines, and the reuse
mechanism is flat copy-and-customize with no inheritance index, no provenance, and no origin/mode
distinction. The one aspect worth a glance is the explicit must-have/optional tiering; the operator
registry could mark certain principles non-waivable per tenant if that distinction proves useful.

### tangential

**Runtime multi-agent frameworks** — AutoGen, LangGraph, CrewAI, and MetaGPT. Out of scope by design:
these are orchestration runtimes, and the model is explicitly not one. The single concept worth a
footnote is MetaGPT's use of standardized operating procedures to structure agent interactions — the
nearest thing to discipline-as-shared-artifact in that space — but it is baked into the runtime rather
than published as a portable, adaptable canon, so it does not teach the model anything about its own
problem. Recent academic work on evidence-based agent protocols (requiring citations to tool outputs,
trace-anchored critiques) echoes the spirit of Hard Evidence but stays at the level of runtime protocol
design.

## 4. Patterns observed

A few meta-observations cut across the sources.

The space conflates two problems. **Intra-repository instruction scoping** (AGENTS.md proximity
cascade, nested `.cursor/rules`) is mature and nearly universal. **Cross-organization reuse of
coordination disciplines with controlled adaptation** is the model's actual subject, and almost no AI-
tooling work addresses it directly. Distinguishing these two in the model's own framing would help
readers who arrive assuming AGENTS.md already covers this.

The dominant reuse mechanism in agent tooling is **copy or install, not live reference**. Templates get
pasted, plugins get installed, rule files get forked. Reference mode — resolve the canonical fresh each
session and treat drift between recorded and current SHA as normal — is rare to nonexistent in agent
tooling. Its only close analogues are in compliance (OSCAL profile resolution) and configuration
management. That makes reference-as-default a genuinely distinctive choice rather than an obvious one,
and worth defending explicitly in the model docs.

The disciplines are convergent. Hard evidence, git-state-as-truth, handoff preconditions, and
documentation-first keep being rediscovered independently — in the rainwater framework, in MetaGPT's
SOPs, in the academic literature on evidence-based protocols. Convergent discovery is strong evidence
the disciplines are real and not idiosyncratic. It also means the content is not where the novelty
lies; the inheritance model is.

The most developed prior art for the **factoring itself** is outside AI entirely — standards bodies
(catalog/baseline/overlay/profile) and platform engineering (public/private module registries). Both
solved publisher/operator/tenant separation, versioning, and controlled local adaptation long ago. The
AI-coordination space has not yet imported those lessons, which is both the gap this project fills and
a source of vocabulary it could borrow.

## 5. Recommendations

Concrete suggestions for the maintainer, in rough priority order. None of these are changes I have made
— they are proposals for the maintainer to weigh.

Acknowledge the structural ancestry. The model document could note, in a sentence or two, that the
catalog/baseline/overlay factoring has well-developed prior art in NIST SP 800-53 / OSCAL and in
Terraform's public/private module registries. This costs little, is accurate, and strengthens the
model's credibility by showing the factoring is battle-tested rather than improvised. Borrowing OSCAL's
clean separation of "what you import" (selection) from "how you modify it" (tailoring) — which the model
already draws as Origin versus Mode — is the single highest-value conceptual import.

Position against the dominant convention. The README would help adopters by stating explicitly that the
model sits *above* their AGENTS.md / CLAUDE.md / `.cursor/rules`, not in competition with them: those
files scope instructions within a repo; this model governs how coordination principles propagate across
repos and organizations. Naming AGENTS.md as the convention adopters already know prevents the common
misread that this overlaps with it.

Cross-reference the closest content peer. A reference to the multi-agent-coordination-framework repo, as
independent corroboration of the disciplines, would both credit parallel work and reinforce the point
that this project's distinctive contribution is the inheritance model rather than the disciplines
themselves.

Consider semantic versioning for canonical principles. Adding a semver alongside the SHA would give
adapted-mode consumers a legible signal for whether a canonical change requires re-translation,
borrowing the discipline Terraform's registry already encodes.

Defend reference-as-default explicitly. Since copy/install is the ecosystem norm and live reference is
the rare choice, the model docs should make the case for reference mode on its own terms (lowest
maintenance, automatic propagation, drift-as-audit-marker) rather than presenting it as self-evident —
readers arriving from the plugin/marketplace world will expect installed copies.

Optionally, treat the marketplace as a distribution channel, not a competitor. If low-friction adoption
matters, the principles could be packaged as a Claude Code skill or plugin for discovery and install,
while the git-native inheritance model remains the system of record for origin, mode, and provenance.
The two are orthogonal: one is how the bytes reach a machine, the other is how adaptation is tracked.

---

*Landscape scan compiled June 2026. Sources reflect the ecosystem at that date and will age; the
findings are a snapshot, not a maintained index.*
