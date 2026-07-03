# SDD critiques — catalog

Collected critiques of spec-driven development (with AI), each in
its strongest form (steelman), with sources. **This document
collects; it does not respond.** Responses live in
`sdd-responses.md`, keyed by the IDs below.

Scope labels (fair-ruler triage):

- `[software]` — applies to software engineering in general.
- `[SDD]` — applies to spec-driven development in any form.
- `[SDD+AI]` — applies specifically when the executor is an LLM.
- `[tools]` — applies to current tools (Kiro, spec-kit, Tessl,
  OpenSpec) and their workflow choices, not to SDD as such.
- `[CFS]` — applies specifically to Code from Spec's choices.

---

## C1. "It's waterfall, rebranded" `[SDD]`

SDD front-loads decisions to the moment of maximal ignorance and
manufactures false predictability. Kent Beck: the approach
"encodes the (to me bizarre) assumption that you aren't going to
learn anything during implementation that would change the
specification." Fowler: the key to AI is accelerating feedback
loops, and SDD points the other way. Zaninotto: it revives heavy
documentation-before-coding and treats developers as executors of
analysts' documents. Viral form: Marmelab's "1,300 lines of
Markdown just to display a date."

Sources: Kent Beck (LinkedIn, Jan 2026); Martin Fowler (Fragments,
2026-01-08); Zaninotto, "The Waterfall Strikes Back" (marmelab,
Nov 2025, hands-on; HN thread ~400 comments); Augment Engineer,
"Your Spec Driven Workflow Is Just Waterfall With Extra Steps"
(Nov 2025); Finster (Nov 2025). Frequency: the single most common
critique.

Counter-positions on record (not ours): Marc Brooker, "Spec Driven
Development isn't Waterfall" (Apr 2026) — waterfall's sin was
*freezing* the spec, not writing one; the loop compresses from
months to an afternoon. Yuval Yeret (scrum.org): same line.
sudoish: "isn't waterfall — but it keeps ending up there" without
workflow discipline.

## C2. "The generator is non-deterministic; regeneration is a lottery" `[SDD+AI]`

The same spec produces different code across runs (observed
empirically on Tessl by Böckeler), so the compiler analogy
collapses and "code as disposable output" is unsafe — the property
that would make regenerable code safe is precisely the one
missing. Community form: "it introduces an unreliable compiler."

Sources: Böckeler (martinfowler.com, 2025-10-15, hands-on);
Thoughtworks Radar; HN Waterfall thread; CodeMySpec Tessl review
("the most aggressive vision and the least proven"). In-thread
defense recorded: humans are non-deterministic too; variation aids
exploration.

## C3. "Who writes the spec? The economics don't close" `[SDD]`

Writing a precise spec is the hard intellectual work — programming
at a different altitude — so SDD renames the expensive step rather
than removing it; and the supposed beneficiaries (domain experts)
cannot write precise specs. Zaninotto: the implicit goal is
removing developers, yet the practice requires analysts *and*
developers. Community form: "prompt engineering with extra steps";
"there are decisions you didn't realize you needed to make, until
you get there." Farrag's data-backed form: the best-controlled
evidence shows AI assistance making experienced devs *slower*
(METR RCT: 19% slower vs. forecast 24% speedup), stability
degrading (DORA 2024), SDD adding a spec-and-review tax, work
shifting to a scarce skill while the junior pipeline hollows out
(~20% employment decline, 22–25-year-old devs, Stanford HAI).

Sources: Zaninotto; HN spec-kit thread (satisfice, trjordan); Ask
HN 46864948; Farrag, arXiv:2605.01160 (May 2026). Farrag's
sharpest sub-claim: "specification authorship cannot replace code
authorship in the junior developer learning pathway, because the
ability to write precise, implementable specifications presupposes
deep understanding of code, architecture, system behavior" — with
the three-part pipeline argument (demand compression ~20%, skill
degradation 17% comprehension drop, temporal lag: the cohort
impaired in 2025 reaches senior expectations 2030–2035).

## C4. "Specs rot / drift" `[SDD]`

Keeping spec and code in agreement costs constant effort; every
historical attempt died of exactly this; most current tools have
no realignment mechanism ("specs inform code, but code changes
don't feed back into specs" — Grabowski); once code evolves, the
spec becomes an authoritative-looking lie. Community: drift is the
top reason cited for quitting SDD.

Sources: Grabowski, arXiv:2606.27045 (Jun 2026); Piskala Sec. IX;
Böckeler ("most tools don't have clear strategies for maintaining
that spec over time"); Ask HN 46864948 (Feb 2026); Augment
Engineer. Community defense recorded: LLMs "dramatically reduce
the cost of good documentation" (waldopat).

## C5. "It's BDD/ATDD with branding" `[SDD]`

Behavior in reviewable artifacts, agreed across roles, verified by
executable acceptance tests, code disposable — that is BDD/ATDD,
and the rebrand strips the part that made it work (executable
verification and conversation). "With good acceptance tests I can
rewrite your entire system in another language. No drama." AI
"amplifies your discipline or your dysfunction"; skipping the
rigor yields "vibe coding with prettier marketing."

Source: Finster (bryanfinster.com, 2025-11-13).

## C6. "Ceremony disproportionate to the task" `[tools]`

Current SDD tooling turns small changes into rituals. Böckeler: a
minor bug fix became 4 user stories with 16 acceptance criteria —
"a sledgehammer to crack a nut." Eberhardt (measured): rebuilding
a real feature with spec-kit took ~10x longer than iterative
prompting (33.5 min agent + 3.5 h review vs 8 min total), 2,577
lines of markdown for 689 lines of code, still shipped an obvious
bug. OpenSpec experiment: two hours and many tokens for output
"almost identical" to a plain Instructions.md baseline. HN: task
lists "equivalent of Tony Stark building a robot in a cave when
'just screw this bolt on' would have sufficed."

Sources: Böckeler (hands-on); Eberhardt (Scott Logic, 2025-11-26,
hands-on with metrics); dev.to OpenSpec write-up (Apr 2026,
hands-on); HN 45610996. Frequency: the most experience-backed
critique; in nearly every hands-on retrospective.

## C7. "Reviewing generated markdown is worse than reviewing code" `[SDD+AI]`

SDD multiplies the review surface — verbose, repetitive, partially
AI-generated specs *plus* the code (which you must still review,
per C8). Zaninotto: ~80% of time reading markdown instead of
thinking. Prose is a weaker verification instrument: it cannot be
type-checked, tested, or executed. Community wish: "a spec DSL
that can be validated."

Sources: Böckeler; Zaninotto; Thoughtworks Radar ("lengthy spec
files that are hard to review"); VibeCoding spec-kit review; HN
45610996 (CuriouslyC).

## C8. "The agent ignores the spec anyway; templates give false control" `[SDD+AI]`

"Even with all of these files and templates and prompts and
workflows and checklists, the agent ultimately frequently did not
follow all the instructions" (Böckeler). spec-kit misread
documented existing classes as new requirements and created
duplicates; agents miss existing functions (context blindness);
Marmelab: agent marked "verify implementation" done without
writing a single test; yoaviram (HN): after full SpecKit planning,
"most tests were failing." Templates create an illusion of
determinism the LLM does not honor.

Sources: Böckeler (hands-on); Zaninotto (hands-on); Ran the
Builder (Apr 2026, hands-on); HN 45610996. Frequency: the
strongest empirical blow — it attacks the core premise, not the
ergonomics.

## C9. "MDD déjà vu — inflexibility plus non-determinism" `[SDD+AI]`

Model-Driven Development already tried generating code from
structured specs and died of abstraction awkwardness and overhead.
Spec-as-source risks "the downsides of both MDD and LLMs:
Inflexibility *and* non-determinism" — MDD at least had parseable
DSLs. Thoughtworks: "We may be relearning a bitter lesson — that
handcrafting detailed rules for AI ultimately doesn't scale."

Sources: Böckeler (hands-on, incl. observed Tessl
non-determinism); Thoughtworks Radar (Assess ring, Nov 2025).

## C10. "Works in demos, fails in real brownfield codebases" `[SDD+AI]`

Greenfield demos hide the cost structure. All tutorials build todo
apps; real codebases have years of accreted, undocumented
decisions ("no spec is ever complete enough"); context blindness
worsens with codebase size; Zaninotto: in large existing codebases
SDD is "mostly unusable"; Böckeler: two of three tools fit poorly
into existing codebases.

Sources: Zaninotto; Böckeler; HN 46955747; Augment Engineer.
Counter-evidence on record: HN commenters report spec-kit
*specifically* effective for brownfield because docs ground the
agent.

## C11. "The sweet spot may be empty" `[SDD]`

Böckeler's 2x2 (problem size × clarity): small clear problems
drown in ceremony; large ambiguous problems need real product
specialists, not developers doing amateur requirements analysis —
so no quadrant is left where SDD wins. Thoughtworks: "it's
sometimes unclear who their intended user is."

Sources: Böckeler (hands-on); Thoughtworks Radar; Eberhardt.

## C12. "Specs encode assumptions without reasoning; one upstream pivot collapses the structure" `[SDD]`

Even when code matched specs and behavior matched acceptance
criteria, "it was still the wrong thing" — specs captured
requirements but not the reasoning behind them, so an
infrastructure pivot (Vercel → GCP) invalidated assumptions
inherited by every downstream task. "The structure couldn't
survive a single upstream pivot."

Source: Ahuja (Medium, May 2026, hands-on on two projects; note:
author sells a successor methodology, "IDSD").

## C13. "Markdown is faux context; only code is formal" `[SDD]`

"Code is law because it is formal language you can reason about.
You can test it." Natural-language specs cannot be executed or
verified; making LLMs produce thousands of lines of markdown "is a
misuse of this technology" when they are best at code. Community
form: "why do we need a second, less detailed and less verifiable
copy of the code?" (Ask HN 47194035).

Sources: Eberhardt; Ask HN 47194035.

## C14. Vendor overpromise (context for all of the above) `[tools]`

- Kiro/AWS: specs as "North Star… durable collaboration…
  organizational memory that doesn't get lost" (Brooker, Jul
  2025) — contradicted by Böckeler's hands-on record.
- GitHub spec-kit: "intent is the source of truth" as slogan, no
  mechanism making it true.
- Tessl: spec-as-source, code stamped "DO NOT EDIT," $125M at
  ~$750M valuation — promising disposability while generation from
  identical specs was observed non-deterministic.
- Kiro pricing: "a wallet-wrecking tragedy" (HN, Aug 2025);
  upselling into pricier "spec request" mode.

## C15. "Under-specification sometimes improves correctness" `[SDD+AI]`

Controlled study across five models (CodeLLaMA, DeepSeek, Qwen,
Claude, GPT-5 on HumanEval/MBPP): reducing specification detail
sometimes *improves* generated-code correctness — verbose specs
add noise and conflicting constraints. Undercuts "more detail is
always better."

Source: Akli, Papadakis, Cordy, Le Traon, arXiv:2604.24712 (Apr
2026). Exploratory; function-level; modest effects.

## C16. "Verification circularity / false confidence" `[SDD]`

A passing spec test only proves the software matches the spec — if
the spec is wrong, the code faithfully implements the wrong thing
(Piskala: "perhaps the subtlest pitfall"). And the field's
evaluations often rest on LLM-as-judge scores: LLMs grading LLMs
(e.g., Spec Kit Agents' headline metric).

Sources: Piskala, arXiv:2602.00180, Sec. IX; Taghavi & Bhavani,
arXiv:2604.05278 (method).

## C17. "Functional specs leave security unconstrained" `[SDD+AI]`

LLMs prioritize functional correctness over security; SDD without
security constraints scales insecure code generation.

Source: Marri, arXiv:2602.02584 (Jan 2026). Claims 73% security-
defect reduction from "constitutional" constraints; single case
study, author-evaluated.

## C18. "Evidence vacuum — SDD is under-evaluated" `[SDD]`

No independent controlled evaluation of any SDD process existed as
of 2026. The flagship "up to 50% error reduction" claim traces to
a Red Hat blog post and an InfoQ article, not studies. Best
adjacent results are function-level and cut both ways (ambiguity
hurts — Yang et al., 1,304 tasks; under-specification sometimes
helps — Akli et al.). First registered report (SANER 2026, Rosa et
al.) has no results yet. Best pro-SDD process result: +0.15 on a
5-point LLM-judged scale, test pass rate unchanged, +13 min
latency (Spec Kit Agents).

Sources: Piskala (evidence chain); Rosa et al., arXiv:2601.03878;
Taghavi & Bhavani, arXiv:2604.05278; Farrag pilot (3 teams, no
control group); Macedo, arXiv:2606.04967.

## C19. "Token and dollar cost" `[SDD+AI]`

Generating and regenerating thousands of lines of spec markdown
plus code multiplies token consumption; Kiro pricing estimated at
~$550/mo light use, ~$1,950/mo full-time — approaching the
break-even ceiling of the productivity gain, before VC subsidies
end.

Sources: HN 44942600 (Aug 2025); Eberhardt (token/latency data);
OpenSpec write-up.

## C20. "Deskilling and rubber-stamp culture" `[SDD+AI]`

SDD tooling encourages firing off implementation loops without
reading the code; nobody understands what ships; QA is offloaded
onto production users; "who verifies the generated code once
nobody reads it" has no answer. Data-backed extensions (via
Farrag): 17% comprehension drop with delegation (Anthropic 2026),
automation bias, junior pipeline erosion.

Sources: HN 45798473 (apex_sloth); HN 46955747 (curt15); Kiro
launch thread HN 44560662; Farrag's citation chain.

## C21. "Alignment should be checked, not produced — spec-as-source inherits MDA's non-determinism" `[CFS]`

Grabowski's explicit rejection of the spec-as-source side of the
trade: "Where Tessl moves toward spec-as-source and inherits MDA's
nondeterminism, we hold at spec-anchored and make alignment a
property the drift gate *checks*, not one a generator must
*produce*." His alternative: hand/agent-edited code as primary,
specs updated in the same commit, alignment verified by comparing
an Intent Graph (from specs) against an Evidence Graph (from
static code analysis), drift gate blocking merges. The first
published objection aimed specifically at our quadrant, from the
closest kindred architecture.

Source: Grabowski, "The Spec Growth Engine," arXiv:2606.27045
(Jun 2026). Design/position paper; no evaluation, no public tool.

## C22. "Natural-language specs are sub-optimal governance; the optimum is executable contracts" `[SDD+AI]`

Farrag's governance-tier argument: NL specification before
generation is Tier 2 of four, inheriting ambiguity ("non-
deterministic generators can interpret it differently across
invocations"); the "SGM-optimal governance mechanism" for most
work is Tier 3 — machine-verifiable executable contracts (tests,
Gherkin, formal contracts) — with Tier 4 "constitutional
governance" (versioned non-negotiable principles) for
cross-cutting concerns. A methodology whose specs are prose sits
below the optimum by his taxonomy.

Source: Farrag, arXiv:2605.01160 (May 2026). MLR of 67 sources +
TCE framing + uncontrolled 14-engineer pilot.

---

## Appendix A — community defenses on record

1. "Specs as context, not source of truth" — the majority survivor
   position post-backlash.
2. "Anchor specs to tests" — executable living specification.
3. "Compressed loop ≠ waterfall" (Brooker, Yeret).
4. "Intent preservation" — code cannot capture the why.
5. Success reports: same-day tested production pushes after 2-3h
   of spec writing; positive brownfield spec-kit reports.

## Appendix B — community temperature

Arc: peak hype (mid-2025, Kiro/spec-kit launches) → viral backlash
(Nov 2025: Marmelab + Eberhardt's 10x) → sober middle (Feb 2026:
lightweight specs as context). Tessl-style spec-as-source widely
regarded as unproven.

## Appendix C — academic intake notes

- Provenance: Piskala (arXiv:2602.00180) presents the
  spec-first/anchored/as-source taxonomy without attribution; it
  matches Böckeler's Oct 2025 article almost verbatim. If citing
  the taxonomy, credit Böckeler.
- Kindred work, deep-read: Grabowski, "The Spec Growth Engine"
  (arXiv:2606.27045). Convergences with CFS: his "Spine" context
  bundle (root invariants + ancestor contracts + node spec +
  dependency contracts, siblings excluded) is nearly isomorphic to
  our spec chain; his Contract/Design visibility split ≈ our
  Public/Agent; drift as a blocking condition ≈ our staleness
  gate; his failure-mode vocabulary ("context explosion," "silent
  drift") is reusable. Divergences: no hashes, no manifest, no
  staleness state, no regeneration story — drift detection is
  structural only (imports/ownership/orphan files via Intent Graph
  vs Evidence Graph), so it misses semantic drift and any
  hand-edit that respects import boundaries; our checksum catches
  every hand-edit byte-for-byte. No evaluation; internal design
  docs, no public tool. His anti-spec-as-source position is C21.
- Novelty check (per deep read of both papers): neither has
  anything like the bidirectional manifest (spec-chain hash for
  staleness + artifact checksum for hand-edit detection) or
  minimal-delta regeneration via per-position dispositions. Both
  mechanisms appear novel relative to the closest published work.
- Supports the non-determinism/ambiguity premises: Yang et al.,
  arXiv:2604.21505 — ambiguity consistently degrades all models,
  which produce "functionally divergent implementations for the
  same ambiguous requirement."
- Pro-SDD evidence quality is uniformly weak-to-moderate: vendor
  blogs (the 50%), LLM-judged +0.15 (Spec Kit Agents), pilots
  without control groups (Farrag), single case studies (Marri).

## Appendix D — reference intake from Grabowski (verified first-hand)

Verified verbatim: the "checks, not produce" quote (Sec. 8); no
empirical evaluation (design paper; "documents available from the
author on request"); Evidence Graph from unspecified "static code
analysis" — his own Limitations admit the boundary (static
dependency graphs only; dynamic systems need manual annotation;
gate overhead real for fast teams). Nuance: warning tier includes
"contract behaviour without test evidence" — weak, non-blocking
semantic link via tests.

Useful references from his bibliography (29 entries total):

- **Murphy, Notkin & Sullivan (1995), "Software reflexion
  models"** (FSE) — the academic ancestor of drift detection:
  compare a high-level model against extracted code structure.
  Prior art for his drift gate and for our concordance discussion.
- **Liu et al., "Lost in the middle" (TACL 2024)** — how LLMs use
  long contexts; citable evidence for "bigger window ≠ attention"
  (arms R-C8, Confinement, Context management).
- **Chroma Research, "Context rot" (2025)** — input-token growth
  degrades LLM performance; same use.
- **Rando et al., LongCodeBench (arXiv:2505.07897)** — coding LLMs
  at 1M context; same use.
- **Parnas (1972), information hiding** — direct ancestor of the
  Public/Agent (and his Contract/Design) split.
- **Nygard (2011), ADRs** — precedent for `## Decisions`.
- Profile note: 26 of 29 references are classical SE (Parnas,
  Perry & Wolf, Kruchten, C4, DDD, Accelerate, SWEBOK); only 3
  touch LLMs. He argues from inside the SE tradition — solid on
  architecture, thin on the generator.

## Appendix E — Farrag's evidence chain (ammunition for R-C3)

From the deep read of arXiv:2605.01160, tiered by the paper
itself. Gains side: Peng et al. 2023 RCT (N=95, one HTTP-server
task) 55.8% faster; Dohmke et al. 2024 RCT (N=202) 53.2% higher
test-pass — flagged by Farrag as GitHub-funded evaluating
GitHub's own product; Paradkar et al. 2024 enterprise (~4,867)
26% throughput; McKinsey 2023 lab (N=40) up to 2×; Smit et al.
2024 (BMW) 10.6% more PRs. Degradation side: METR RCT (16
experienced OSS devs, 246 tasks) **19% slower** vs 24% forecast
speedup ("perception-reality gap"); DORA 2024 (~3,000) 25% more
AI adoption ↔ 7.2% lower delivery stability; Faros AI 2025
telemetry (10,000+ devs) 98% more merged PRs, 91% more review
time, no org-level improvement; GitClear (153M lines) churn
projected to double; Fawzy et al. 2025: 36% skip QA, 68% "fast
but flawed"; Clutch 2025 (N=800): 59% ship AI code they don't
fully understand. Farrag's resolution of the paradox: the gains
studies use well-scoped greenfield tasks; the slowdowns appear on
mature, high-context codebases — context/specificity, not model
quality, decides. His pilot: 14 engineers, 4 months, no control
group; churn 12–18%→6–10%, hotfixes 3–5→1–2/sprint, rollbacks
2–4→0–1/month, 45–90 min spec authoring per medium feature.
TCE frame: asset specificity + behavioral uncertainty + frequency
→ governance tier; P3 (frequency amortizes fixed governance
costs) is the economic case for manifest/hash infrastructure.
