# AGENTS.md

## Site content summary

> **Maintenance rule**: whenever content is added, removed, or
> substantially revised (new entries, updated descriptions, changed
> structure), update this section to match. This summary is the
> starting point for every new session — it must reflect the
> current state of the site.

This is the website for **codefromspec.com** — a static site
(HTML + CSS, no JS, no build step) presenting the Code from Spec
methodology. Author: Gustavo Silveira Neto.

Content lives in `content.md` files alongside each leaf node's
`_node.md`. The content files are inputs to generation (via
`input: EXTERNAL/...`), not specs themselves.

### Home (`/`)

- **File**: `code-from-spec/html/home/content.md`
- **Last updated**: July 3, 2026
- **What it says**: Introduces Code from Spec — specs are the
  source of truth, code is a derived artifact. Describes the
  spec tree (inheritance, declarations), confinement (agents
  see only the chain), staleness by hash, and verification
  (tests as independent opinion). Reports current status: spec
  format approaching definitive form, disposition-guided
  regeneration built and promising. Links to journal and
  rationale.

### Rationale (`/rationale`)

- **File**: `code-from-spec/html/sections/rationale/content.md`
- **Date**: April 2026 (frozen — do not update)
- **Editorial decision**: The rationale is preserved as the
  original argument that launched the project. Its framing is
  intentionally simpler than the theory that came later — it
  promises a destination without the vocabulary to analyze
  whether it is reachable. That naivety is part of the public
  record: the reader sees where the thinking started, then
  follows it to the theory. Do not revise the rationale's
  content to match later conclusions; instead, the note at
  the top links to `/theory` for where the thinking arrived.
- **What it says**: Why the methodology exists. The knowledge
  gap (domain experts can't put knowledge into software
  directly). What AI changed (economics of deriving code from
  spec) and didn't (expert still can't evaluate output).
  Why specs failed before (formal methods: precision without
  participation; CASE/MDA: round-trip problem never solved;
  both died of economics). Why economics changed now (generation
  cheap, round-trip closed by construction, natural language
  usable). The destination: engineering team becomes platform
  team for the whole company, domain experts contribute to
  specs within guardrails. What happens to engineers (more
  engineering, not less — architecture, constraints, failure
  modes). Unsolved problems: natural language ambiguity,
  non-determinism, domain-expert authorship still aspirational.

### Journal (`/journal`)

- **Index file**: `code-from-spec/html/sections/journal/index/content.md`
- Five entries (newest first):

#### Growing pains (July 19, 2026)

- **File**: `code-from-spec/html/sections/journal/entries/growing-pains/content.md`
- **What it says**: Spent ~3 weeks writing the theory essay.
  The result exposed dissonance with published content: "spec
  is source of truth" is incomplete (the pair is the unit of
  truth); domain-expert participation is complicated by spec
  drifting toward prompt; the artifact is not disposable. The
  essay gives a vocabulary to *analyze* the rationale's
  destination rather than just promise it. Rationale frozen
  with note, landing page updated. Next: sources companion
  page, revisiting project objectives with the theory's
  vocabulary, digesting lessons for the framework.

#### Telling the agent what changed (July 1, 2026)

- **File**: `code-from-spec/html/sections/journal/entries/telling-the-agent-what-changed/content.md`
- **What it says**: The fix for anchoring. Built a cache that
  records chain content by hash per position. On regeneration,
  compares old vs. new chain position-by-position and tags each
  with a disposition (unchanged/changed/added/removed). Changed
  positions deliver old content before current spec. Results:
  agent reliably processes changes it previously missed.
  The "position order" hypothesis (code before spec) was
  tested and produced nothing — order alone didn't fix
  anchoring; the disposition signal was the actual fix.

#### Anchoring on old code (June 16, 2026)

- **File**: `code-from-spec/html/sections/journal/entries/anchoring-on-old-code/content.md`
- **What it says**: The tension: showing the agent its previous
  output provides stability, continuous improvement, and
  codebase maturity — but risks anchoring (agent preserves
  old code instead of applying spec changes). Three hypotheses
  for why (inverted reasoning, positional bias, silence
  pre-answered). Anchoring is hard to detect — looks like
  "no changes needed." Why keep the artifact anyway:
  generation from scratch has no convergence mechanism.
  Open problem: generate with artifact but eliminate anchoring.

#### Confinement (June 10, 2026)

- **File**: `code-from-spec/html/sections/journal/entries/confinement/content.md`
- **What it says**: The generation agent sees only the spec
  chain and writes one output file. Why this matters: removes
  "the third option" (agent inventing context that looks like
  research). Ambiguity becomes signal — failures trace to
  specific chain gaps, fixable permanently. Trust the
  framework (hash + confinement + tests), not the agent.
  One deliberate exception: existing artifact shown during
  regeneration (fenced carefully).

#### Context management by construction (June 8, 2026)

- **File**: `code-from-spec/html/sections/journal/entries/context-management/content.md`
- **What it says**: Code is a poor context source (mechanism,
  not intent). Industry builds retrieval machinery (RAG,
  embeddings); Code from Spec gets context as a byproduct of
  tree structure. Inheritance = guaranteed context, not
  retrieved. Nobody designs context — place node in tree,
  declare dependencies, assembly follows. Rules written once
  at ancestor nodes reach all future leaves. Context compounds
  across sessions (spec changes persist, unlike ephemeral
  conversation state).

### Theory (`/theory`)

- **Index file**: `code-from-spec/html/sections/theory/index/content.md`
- **Date**: July 18, 2026
- **What it is**: A multi-part essay building a vocabulary for
  spec-driven development — what follows once a stochastic
  generator exists. Seven parts:

#### Part I — The primitives

- **File**: `code-from-spec/html/sections/theory/part-i/content.md`
- **What it says**: Six primitives: space of texts, intent,
  description, generator (with prior), oracle, artifact.
  Four operations: sampling, local move, resampling,
  ratifying. Aiming (description) is statistical; confirming
  (oracle) is bounded. Bug taxonomy: nonconformance,
  underspecification, mis-specification — each has a different
  repair. Anchoring defined with three participants (point,
  generator, description). Comments as unratified description
  fragments embedded in artifacts.

#### Part II — The apparatus at its limits

- **File**: `code-from-spec/html/sections/theory/part-ii/content.md`
- **What it says**: Pushes each primitive to extremes. Settled
  vs. moving intent. Absent description (artifact becomes
  pseudo-spec, anchoring with no counter-force). Perfect
  description (nonconformance survives). Generator limits
  (no prior = noise; frozen prior = unsteerable). Oracle
  limits (description-facing can't catch mis-spec; intent-
  facing requires settled intent). Cost limits (zero anchoring
  eliminates resampling need; full anchoring forces it).
  The corner current practice occupies: no durable description,
  free generation, cheap but partial checking — a machine for
  shipping plausible code whose unchecked dimensions nobody
  chose.

#### Part III — The description

- **File**: `code-from-spec/html/sections/theory/part-iii/content.md`
- **What it says**: The spec is a delta — what remains after
  subtracting what the generator's prior already resolves.
  Silence is compression, not omission. Source code is spec at
  the limit (decides every dimension). Diminishing returns per
  clause. Authoring cost bundles three prices (deciding,
  knowing the prior, wording). The generator is not part of the
  description (tendency vs. predicate). Spec converges toward
  a prompt under loop pressure. Authored form vs. delivered
  form (presentation is a design surface). The artifact is not
  disposable (holds resolutions and evidence the description
  never recorded). SDD defined: keeping description, artifact,
  and declared generator aligned.

#### Part IV — The oracle

- **File**: `code-from-spec/html/sections/theory/part-iv/content.md`
- **What it says**: Type system as pre-paid oracle. Closed vs.
  open oracles (compiler trusted; fresh test not). Linter,
  formatter, scanner as pre-paid family. When intent moves
  (oracle resistance, bulk-update danger). Adversarial
  generator (retrying until green ≠ satisfying intent).
  Feeding verdicts back (safe only when oracle is closed).
  Production as ultimate oracle (late, expensive, faces intent
  directly). Can't build an intent-facing oracle (authoring
  always interposes). LLM-as-judge: catches sampling accidents
  only, blind to underspecification, niche shrinks with better
  generators, 'pass' worth nothing, prone to being persuaded.

#### Part V — The test suite

- **File**: `code-from-spec/html/sections/theory/part-v/content.md`
- **What it says**: Tests fill the gap no other oracle covers
  (behavioral, mechanical, cheap, project-specific, every
  draw). Decisive property is memory — pins lessons
  mechanically. Tests generated too (same risks as generated
  code). Independence determines power (separate spec, separate
  generator, separate author = more independence). Generator
  swap doesn't break tests but may misalign coverage. Fix goes
  in both spec (broad, probabilistic) and test (narrow,
  certain).

#### Part VI — The case for decomposition

- **File**: `code-from-spec/html/sections/theory/part-vi/content.md`
- **What it says**: Real systems are heterogeneous on every
  axis. Decomposition lets each component sit at its own
  optimum. Three axes for cuts: settled/moving intent,
  conventional/unconventional, cheap/expensive verdicts.
  Boundary is real when: no shared mutable state, narrow
  interface, isolated failure domains, oracle covers both
  sides. Reuse (one draw, shared maturity), encapsulation
  (defense against Hyrum's Law), single responsibility
  (fewer load-bearing dimensions). Cycle: good boundaries
  make SDD safe, SDD makes boundaries affordable — but false
  boundaries amplify defects. Decomposition wisdom is
  load-bearing, not quaint.

#### Conclusion

- **File**: `code-from-spec/html/sections/theory/conclusion/content.md`
- **What it says**: The vocabulary (intent, description,
  generator, oracle, artifact) describes any software, not just
  AI-generated. Two payoffs: the vocabulary itself (for
  thinking and deciding) and a bet (conclusions strong enough
  to build on). Irreducible roles: someone holds intent and
  authors the delta; someone delivers verdicts that answer to
  intent. Everything between can move to the machine. Remove
  both poles and you get a loop optimizing conformance to
  encodings nobody checks against intent.

### License (`/license`)

- **File**: `code-from-spec/html/sections/license/content.md`
- CC BY 4.0 International. Copyright 2026 Gustavo Silveira Neto.

---

## Publishing a journal entry

Journal entries live under `code-from-spec/html/sections/journal/entries/`.
Each entry is a leaf node with a `content.md` alongside its `_node.md`.

### Steps

1. Create the node directory under
   `code-from-spec/html/sections/journal/entries/<slug>/`.

2. Create `_node.md` following the pattern of existing entries:

   ```yaml
   ---
   depends_on:
     - SPEC/design/journal/template
   input: EXTERNAL/code-from-spec/html/sections/journal/entries/<slug>/content.md
   output: public/journal/<slug>/index.html
   ---

   # SPEC/html/sections/journal/entries/<slug>
   ```

3. Create `content.md` with frontmatter and body:

   ```markdown
   ---
   title: <Entry title>
   description: "<One-line summary for meta and journal index>"
   date: <YYYY-MM-DD>
   ---

   ![<Entry title>](/images/journal/<image-file>)

   # <Entry title>

   <Month Day, Year>

   <body>
   ```

4. Add the entry to the index at
   `code-from-spec/html/sections/journal/index/content.md`.
   Insert in chronological order (newest first). Format:

   ```
   - [Title](/journal/<slug>) — <Month Day, Year> — <description>
   ```

5. Update `public/sitemap.xml` — add the new URL with
   `<lastmod>` date. Also update `<lastmod>` for the
   journal index entry (it will change too).

6. Run `/cfs-status` to confirm staleness. Expect at least
   the new entry (missing) and the index (stale).

7. Generate with `/cfs-generate`.

---

## Static files

`public/robots.txt` and `public/sitemap.xml` are static
files, not generated artifacts. They live outside the spec
tree and must be updated manually.

- **`robots.txt`** — rarely changes. Points crawlers to
  the sitemap.
- **`sitemap.xml`** — must be updated whenever a page is
  added, removed, or has its content substantially revised
  (update `<lastmod>` dates accordingly).
