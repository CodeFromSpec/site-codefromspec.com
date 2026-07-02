# SDD analysis — session handoff

Context document for resuming the SDD critique-analysis work in a
fresh session. Read this first, then the files in "Read next".

## Resume prompt (paste into a new session)

```
Vamos retomar o trabalho de análise das críticas ao SDD.
Leia drafts/sdd-analysis-handoff.md e siga a seção "Read next"
de lá. Depois me dê um resumo de uma linha do estado e vamos
atacar o próximo item da lista de open work.
```

## What this work is

We are building Code from Spec's answer to "how do we show SDD is
not just hype": collect every critique of spec-driven development
(with AI) from academia, industry, and community; steelman each
one; respond by ID. Publication form is deliberately NOT decided
yet — collect and respond first.

## Read next (in order)

1. `drafts/sdd-critiques.md` — the catalog: C1–C22, steelman +
   sources only, no responses. Appendices: community defenses,
   field temperature, academic intake notes, Grabowski reference
   intake (verified first-hand).
2. `drafts/sdd-responses.md` — responses by ID (R-C1–R-C22), with
   response types (CONCEDE / DISSOLVE / REFUTE / PENDING),
   position map, and the open-work list.
3. `drafts/sdd-definition.md` — our definition page draft
   (premises → obligations → what it dissolves): the conceptual
   backbone the responses lean on.

Read on demand (not upfront): the published site
(codefromspec.com — home, rationale, articles) and remaining
drafts in `drafts/articles/` (see `order.md` there for the
publication queue). The methodology spec lives in
`../framework/` (CODE_FROM_SPEC.md, rules/, docs/TESTING.md).

## Key concepts this work relies on (from the corpus)

- **The pair**: source of truth = (spec, artifact); spec holds
  authority, artifact records how delegated choices were resolved
  ("collapse"). Generator is a sampler, unpinnable.
- **Minimal-delta regeneration** with per-position dispositions
  (cache + hash comparison); ordering/recency hypothesis tested
  and refuted; disposition works (~10 correct regenerations across
  projects, not systematic measurement).
- **Independence by structure**: test nodes see contracts only;
  `# Agent` is unimportable; blind passes.
- **SDD definition (ours)**: spec-driven iff the pair is kept in
  sustained, mechanically detected concordance. Spec-first =
  "spec-started", a well-crafted prompt. Anchored vs as-source =
  distinction without a difference.
- **Fair ruler**: every critique triaged by scope —
  [software]/[SDD]/[SDD+AI]/[tools]/[CFS]; only the last three
  are ours to answer.

## State (as of 2026-07-02)

Done: three survey agents (academia, industry, community) ran and
were merged; Grabowski (arXiv:2606.27045) and Farrag
(arXiv:2605.01160) deep-read; Grabowski verified first-hand
(quotes confirmed; references extracted into Appendix D).

Open work (from sdd-responses.md):

1. **R-C1 strong forms** — "early decision under maximal
   ignorance" and "false predictability" never answered; partial
   lead: regeneration flattens the cost-of-change curve.
2. **R-C3 economics/who-writes** — the hardest pending; armed
   with Farrag's data on both sides; includes the junior-pipeline
   sub-critique ("spec authorship presupposes code
   understanding") which deserves honest concession + the
   platform-team partial answer.
3. **R-C4 real form** — is the reconciliation cost payable in
   practice; empirical, accumulates with the record.
4. **R-C11** — write the permanent-tree answer to Böckeler's 2x2.
5. **R-C21/R-C22** — fold into the future revision of
   `drafts/articles/the-llm-is-not-a-compiler.md` (Grabowski as
   named opponent; Farrag's tiers in the verification section).
6. Possible contact with Grabowski (user was reading the paper;
   his docs are "available on request"). Conversation angles:
   (a) his Spine ≈ our chain, his Contract/Design ≈ our
   Public/Agent — same discovery, independent; (b) what we offer:
   the pair mechanism (chain hash + checksum) is adoptable in his
   model even if he never accepts spec-as-source — his structural
   drift gate misses semantic drift and boundary-respecting hand
   edits, and his design stores no record of which spec version
   produced which code; (c) the disposition record answers his
   non-determinism objection with data, not argument — we never
   needed deterministic generation, only detectable divergence +
   bounded regeneration; (d) opening question: how is the
   Evidence Graph constructed in practice (his own Limitations
   admit static-graphs-only); (e) possible outcome: public
   exchange (post + response) as the field's way out of the
   hype/backlash cycle.

## Epistemic calibration rule (for the essay revision and all responses)

Three degrees of claim strength — never dress one as another:

1. **Operationalizable prediction** — e.g., the diff-minimality
   experiment (brownfield: smaller diffs for the same spec change
   → fewer regressions than higher isolated code quality with
   bigger diffs). The only degree that may gesture at
   falsifiability, and only with the experiment stated.
2. **Retrospective explanation** — e.g., regeneration-from-scratch
   explains demos-impress/production-disappoints. Defended by
   parsimony, not experiment.
3. **Lens / reframing** — e.g., "the LLM is a dev, not a
   compiler"; "SDD is a gradient." Judged by fecundity, not
   truth.

Never use the word "falsifiable" without shipping the experiment
alongside. The skeptical reader discounts all credibility on that
one inflation.

## Working style (how the human wants this done)

- Debate before writing: propose structure/position, get
  agreement, then draft.
- Steelman always; concede with precision where the critique is
  right — calibration is the anti-hype strategy (never cite the
  "50%" claim; publish failed hypotheses).
- Conversation in Portuguese; all written artifacts in English.
- Collection and response stay in separate documents, keyed by ID.
- Don't think about publication format until told.
- Never run git commands; drafts live in `drafts/`, spec changes
  only with explicit approval.

## Useful references discovered

- Murphy/Notkin/Sullivan 1995 "Software reflexion models" —
  academic ancestor of drift detection.
- Liu et al. "Lost in the Middle" (TACL 2024), Chroma "Context
  Rot" (2025), LongCodeBench — citable evidence for "bigger
  window ≠ attention" (arms R-C8, Confinement, Context
  management).
- Böckeler owns the spec-first/anchored/as-source taxonomy (Oct
  2025); the Piskala paper uses it uncredited.
