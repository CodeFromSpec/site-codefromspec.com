# Publication order

Order for the prepared drafts in this directory. The sequence is
constrained by internal links — each article only links to
articles published before it.

| # | Draft | Links to (must already be live) |
|---|---|---|
| 1 | `telling-the-agent-what-changed.md` | anchoring-on-old-code (published) |
| 2 | `collapsing-the-wave-function.md` | anchoring, telling |
| 3 | `many-worlds.md` | collapsing, telling |
| 4 | `the-immune-system.md` | anchoring, telling, many-worlds; framework TESTING.md (external, live) |
| 5 | `the-escape-hatch.md` | collapsing |

Notes:

- `the-escape-hatch` only depends on `collapsing`, so it may be
  moved anywhere after #2 if pacing calls for it. The order above
  follows the narrative arc: fix → theory → consequence →
  verification → practice.
- Suggested index lines (one per article, in the style of the
  existing articles index):
  - **Telling the agent what changed** — The fix for anchoring: a
    cache, a hash comparison, and a one-word disposition per spec
    position. What worked, and the elegant hypothesis that
    produced nothing.
  - **Collapsing the wave function** — A spec defines a space of
    programs; the artifact records which one you got. Why the
    source of truth is the pair, and why generated code is not
    disposable.
  - **Many worlds** — Every program carries its own bugs.
    Regenerating from scratch samples a new set each time — why
    code stability is a statistical property, not sentimentality.
  - **The immune system** — Tests were never the last line of
    defense; they were the second. Generation removes the first —
    and makes test independence something the structure must
    enforce.
  - **The escape hatch** — Nothing forbids writing code inside a
    spec. On the abstraction ladder, the prohibition that was
    never about authorship, and why trust in prose flows down
    from the bottom rung.

Not yet scheduled (drafts still being worked):

- `the-model-is-part-of-the-spec.md` — needs reconciliation with
  collapsing (territory split) before queueing; natural slot is
  after the-immune-system (its closing pairs with it).
- `on-living-in-git.md` — pending consolidation (absorbs
  from-artifact-tags-to-a-manifest-file).
- `from-artifact-tags-to-a-manifest-file.md` — to be absorbed and
  deleted.
- `the-llm-is-not-a-compiler.md` — essay, other register; pending
  major revision (Finster/Grabowski sections, references,
  epistemic calibration); may not be a site article at all.
