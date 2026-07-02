# SDD critiques — responses by ID

Responses to the critiques cataloged in `sdd-critiques.md`, keyed
by ID. Working document.

Response types:

- `CONCEDE` — true; open problem; we say so.
- `DISSOLVE` — category error; evaporates under a precise
  definition.
- `REFUTE` — we have mechanism, evidence, or a report.
- `PENDING` — no answer yet.

---

## R-C1 — waterfall `PARTIAL: dissolve + pending`

The label dies when you ask *which* waterfall defect. Of the five
(early decision under ignorance, slow feedback, cost-of-change
curve, handoff context loss, false predictability):

- **Slow feedback: escaped decisively.** The loop is hours. Beck's
  "you won't learn anything during implementation" describes
  write-once spec workflows; in ours, the convergence loop is
  precisely *learn during implementation, deposit the learning in
  the spec, regenerate*. Our specs are not written before
  learning; they are where learning accumulates. (Allies: Brooker,
  Yeret — compressed loop is the iteration.)
- **Handoff context loss: inverted.** The spec is what survives
  handoffs; the chain is the context, chosen and versioned.
- **Cost-of-change curve: partially answered.** Regeneration
  flattens the curve that made early decisions fatal; disposition
  makes change cheap and targeted. Needs development.
- **Early decision under maximal ignorance: PENDING.** Honest
  partial: decisions in the tree are cheap to revise, and leaf
  detail can be added late (progressive refinement, layers). But a
  root-level architectural bet is still a bet made early.
- **False predictability: PENDING.** The manifest tells you the
  code came from the spec — it does not tell you the spec is
  right. Related to R-C16's concession.

## R-C2 — non-determinism `REFUTE (absorbed as premise)`

We accepted the premise fully and built on it: the pair as source
of truth (Collapsing the wave function), stability as statistics
(Many worlds), minimal-delta regeneration (Telling the agent what
changed), behavioral verification (The immune system). Definition
page, premise 1. Böckeler's Tessl observation is *evidence for our
premise* — file under support. The critique is correct against
disposability-SDD and answered by design in pair-SDD.

## R-C3 — economics / who writes `PENDING`

Untouched, and now armed with data on the critique's side (METR,
DORA, junior pipeline). Notes for the eventual answer: (a) the
authoring-cost vs verification-cost trade is per-passage, not
global (escape hatch dial); (b) Farrag deploys the same evidence
*in favor of* spec governance — his thesis is our thesis
("specification discipline, not model capability, is the binding
constraint"), and his pilot numbers (churn 12–18%→6–10%, hotfixes
and rollbacks down 60–75%, at 45–90 min spec authoring per
feature) are the field's first cost-benefit sketch; (c) the
north-star honesty already concedes domain experts can't author
precise specs today — guardrails are the proposed path, unproven;
(d) our own economics claim must stay narrow: tokens vs
engineer-days for the same consistency guarantee; (e) the
junior-pipeline sub-critique ("spec authorship presupposes deep
understanding of code") deserves its own honest treatment — it is
probably true, it applies to the whole field, and our
platform-team frame partially answers it (the tech team remains
the locus of that understanding; domain experts contribute inside
rails, not instead of engineers). Needs real work.

## R-C4 — spec rot / drift `DISSOLVE (as stated) + PENDING (real form)`

As stated, tautology: drift is the name of the state where the
reconciliation cost stopped being paid, and SDD *is* the activity
of paying it (Definition, obligation 3). Mechanically: hash
staleness in both directions (stale / modified); cheap generation
is what changed the economics that killed formal methods and
CASE/MDA (rationale). The real remaining question — **is the
reconciliation cost low enough in practice, and do agents raise or
lower it?** — is empirical. Current answer: the disposition
record (~10 regenerations across projects, all correct; not
systematic measurement). Community's own defense agrees: LLMs
cheapened documentation maintenance.

## R-C5 — BDD with branding `CONCEDE half + REFUTE half`

Concede proudly: the discipline is old; the lineage (BDD/ATDD,
tests as executable specs) is an asset. Refute the operative half:
"with good acceptance tests I can rewrite the system, no drama"
assumes a replay you can trust — a human or a compiler. A sampler
reintroduces the drama disposability assumes away: the ratified
survives the re-roll, the luck does not, and no suite pins
everything (Many worlds). Same discipline, different executor —
and the difference changes which old truths still hold.

## R-C6 — ceremony `CONCEDE for the field + REFUTE for CFS`

True of per-task spec ceremony (spec written per change, then
discarded or filed). In CFS the tree is permanent: a bug fix is a
small diff to an existing node; the ceremony was paid once, at
tree construction, and amortizes across every change. Eberhardt's
10x measures exactly the non-amortized case. Honest caveat: tree
construction is the up-front cost, and C3 asks who pays it.

## R-C7 — markdown review burden `PARTIAL`

Their markdown is AI-generated per task; our specs are the durable
authored artifact — reviewing them IS the work, not overhead on
top of it. Noise control is a design requirement: no comments, no
metadata in artifacts, manifest absorbing churn, minimal diffs
(on-living-in-git). Review altitude is earned locally — spec-level
review where tests are strong, code-level where they are not. The
"validatable spec DSL" wish is partially answered by test specs
(executable) and format validation (validate_specs). Needs
dedicated treatment when on-living-in-git is consolidated.

## R-C8 — agent ignores the spec `REFUTE (home turf)`

Correct about undifferentiated context-stuffing — and it is why
confinement and curated chains exist. Attention is managed by
subtraction: the chain contains only what governs the artifact;
compliance is structural ("an agent's compliance is only as good
as its chain"); disposition directs attention mechanically (the
two-word-change experiment: missed without disposition, applied
with it). Contrast to cite: their agents receive template piles;
ours receive a bounded chain where every position is load-bearing.
Evidence: Confinement, Context management, Telling.

## R-C9 — MDD déjà vu `REFUTE (published)`

Rationale names CASE/MDA as the second failed wave, the cause of
death (reconciliation economics; round-trip never solved), and
what changed (round-trip closed by construction; no-hand-edit
invariant; cheap regeneration). Non-determinism half → R-C2. The
"bitter lesson" line deserves a direct response eventually: the
tree is not handcrafted rules *for the AI* — it is the knowledge
of the system, and the AI is downstream of it.

## R-C10 — demos vs brownfield `REFUTE by agreeing + evidence needed`

The greenfield sampling-bias argument is our own thesis (essay;
Many worlds): regeneration-from-scratch's blast radius is
invisible precisely where there is no past to destroy. The
brownfield answer: minimal-delta + the pair + extraction layers;
first evidence is the production-service merge (200+ spec files,
111 artifacts, build green). Record needs to accumulate — this one
is won by history, not argument.

## R-C11 — empty sweet spot `PENDING (answer sketched)`

The 2x2 is drawn for per-task specs. With a permanent tree the
unit is the system, not the task: small changes ride the existing
tree (no ceremony); large ambiguous problems are decomposed *by*
the tree (progressive refinement). The "who is it for": the
platform-team frame — engineers build spec infrastructure, domain
experts contribute within it. Needs writing.

## R-C12 — assumptions without reasoning; upstream pivot `REFUTE for CFS`

This is what the tree is for. An upstream pivot is an
ancestor-node edit: staleness cascades mechanically to every
affected descendant, each regenerated or verified ("no change
required" as an agent-verified outcome — the server case in
Telling). Reasoning lives in node intent sections and
`## Decisions`. Devastating against flat per-task specs; answered
by inheritance + cascade. Honest caveat: cascade correctness at
scale is where our record is thin (inherited-constraint changes —
the untested scenario named in Telling).

## R-C13 — only code is formal `REFUTE half + CONCEDE half`

The executable-meaning anchor exists: test specs compile to
executable verification — meaning is pinned mechanically exactly
where it matters (Immune system). Formality is a dial, not a
genre: types, schema constraints, literal code in the spec where
precision demands (Escape hatch). Concede: prose that is neither
tested nor typed is unverifiable — that is our weak-type-seams
argument stated from the other side. We agree with the physics and
route around it.

## R-C14 — vendor overpromise `(context, not critique)`

Use as contrast: every claim on our site is either mechanism
(inspectable) or report (dated, failures included). None is
promise. We never say "North Star," we say "north star, not a
current capability."

## R-C15 — under-specification helps `REFUTE by agreeing`

We never held the maximal-detail premise. The spec stays at intent
altitude; shared competence is deliberately delegated to the model
(the default sink as feature); over-specification collapses specs
into pseudo-code and forfeits legibility; dilution is a named,
monitored failure mode. Akli et al. is evidence for calibrated
altitude — our design — against maximal detail — the tools'
design.

## R-C16 — verification circularity `CONCEDE half + REFUTE half`

Spec↔reality: conceded in print — the methodology makes the system
faithful to the spec, not to reality; that gap is a human problem
no framework automates. Mitigation (not solution): specs legible
to the domain, reviewed before code exists.
Implementation↔test circularity: structurally addressed —
independence by construction (blind passes, contract-only
visibility; Immune system, TESTING.md), verification is executable
behavior, never LLM-as-judge.

## R-C17 — security unconstrained `REFUTE for CFS`

Guard nodes: security policy written once at an ancestor,
inherited by every descendant, enforced around contributors who
cannot evaluate it. Marri's "Constitution" is a guard node by
another name — independent convergence on the mechanism. Evidence:
the path-traversal boundary-check bug found in spec review before
generation. Caveat: the mechanism exists; its content is
discipline.

## R-C18 — evidence vacuum `CONCEDE fully (and weaponize)`

Applies to us with full force: our record is reports and
mechanisms, not controlled measurements, and we say so in print.
The position: never cite the 50%; publish failed hypotheses; keep
proposing the experiment that would kill our own thesis (diff
minimality vs regressions). In a field whose strongest claims
trace to trade press, calibration is the differentiator.

## R-C19 — token cost `CONCEDE openly + reframe`

Cost is real and we publish it (3.5M tokens for the merge session;
500k burned by a mid-generation spec change — with the lesson).
The comparison is not tokens vs free; it is tokens vs
engineer-days for the same consistency guarantee, and the
methodology assumes a user willing to pay. Cost discipline is
designed in: no comments, manifest (no tag-only churn),
disposition (no exploratory re-reads), 3–5k-token chains instead
of repository dumps.

## R-C20 — deskilling `REFUTE half + CONCEDE half`

Refute: "nobody reads it" is not the model — review altitude is
earned locally; verification is executable and independent; "who
verifies" has a named pipeline for an answer. Concede: the human
who stops reading code loses something real; the methodology moves
the skill upward (spec precision, verification design, failure
diagnosis) on purpose. Whether that trade hollows the profession's
junior pipeline is a field-level question no methodology answers
alone.

## R-C21 — "alignment checked, not produced" (Grabowski) `REFUTE (the best kind: a real opponent)`

His objection assumes spec-as-source requires deterministic
generation — that a generator must *produce* alignment. We claim
neither. We claim **detectable divergence plus bounded
regeneration**: the manifest records the pair (chain hash +
artifact checksum), divergence is caught in both directions, and
regeneration is minimal-delta under dispositions, verified by
behavioral tests. "Produced" vs "checked" is a false dichotomy —
we check, mechanically, *and* regenerate, boundedly.

And the comparison of the two checkers favors ours. His drift gate
compares structural facts only (imports, exports, file ownership):
it cannot see semantic drift, and cannot see a hand-edit that
respects import boundaries. Our checksum catches every hand-edit
byte-for-byte; our chain hash catches every spec movement; tests
catch the semantics. His architecture also has no staleness state
and no record of which spec version produced which code — the
collapse information (Collapsing) simply isn't stored anywhere in
his design.

What to take from him rather than argue with: the Spine (≈ our
chain — independent convergence on confined, ancestry-scoped
context is strong validation), the Contract/Design split (≈
Public/Agent), and the vocabulary ("context explosion," "silent
drift"). The disagreement is exactly one axis: who may edit code.
His answer reintroduces the round-trip problem he inherited from
spec-anchored; ours removes it by construction.

## R-C22 — "NL specs are Tier 2; optimum is executable contracts" (Farrag) `REFUTE by classification`

The tier assignment misreads the architecture: CFS is not a
Tier-2 system, it spans Tiers 2–4 by design. The prose spec
carries intent (his Tier 2 — legibility is the point there); the
test specs are executable contracts, generated and run per
behavior (his Tier 3 — and independence-by-construction is
stronger than his tier requires); the guard nodes and root
constraints are exactly his Tier 4 "constitutional governance,"
versioned and inherited (convergent with Marri's Constitution,
C17). His own P3 (frequency amortizes fixed governance costs) is
the economic argument for our manifest/hash infrastructure. Where
his critique lands: a node whose behavior is pinned by neither
type nor test sits at Tier 2 exposure — which is our
weak-type-seams doctrine (Immune system) said in his vocabulary.

---

## Position map (from Appendix B of the catalog)

We are not the hype (mechanisms and dated reports, no promises),
not the backlash (we concede its true half — most of it is aimed
at per-task ceremony and disposability, and lands), and not the
sober middle (which, by our Definition, stopped doing SDD and kept
the vocabulary: "specs as context" is spec-first, a well-crafted
prompt). We are a fourth point: spec-as-source economics with the
pair instead of disposability — the position the backlash never
tested.

## Open work

- R-C1: develop early-decision and false-predictability answers.
- R-C3: the economics answer — the hardest and most valuable.
- R-C4: reconciliation-cost evidence over time.
- R-C11: write the permanent-tree answer to the 2x2.
- R-C21/R-C22: fold into the essay revision (Grabowski is the
  named opponent the essay's "reconciler" section anticipated;
  Farrag's tiers belong in the verification discussion).
