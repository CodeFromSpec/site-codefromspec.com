# Future work: publish the AI "Testimonials" section

Status: decided, not started. This file is the briefing for the
session that implements it. Read it fully before doing anything.

## What this is

Publish a new section on the site, sibling of `/articles`, with
the AI-written session testimonials that currently live in
`drafts/testimonials/`. Decision made on 2026-06-11 after
discussion; the framing below is settled — do not relitigate it,
but do ask the human before deviating.

## The framing (the most important part)

The section is **entertainment, not endorsement**. The
testimonials are self-reports by an AI about working under the
methodology — not an independent witness, and we do not pretend
otherwise. The credibility problem (AI praising the methodology
it was instructed to follow reads as astroturfing) is solved by
self-aware lightness, not by a defensive disclaimer.

- Section name: `AI "Testimonials"` — with the quotes; the irony
  is deliberate and part of the joke. Use curly quotes in HTML
  (`&ldquo;Testimonials&rdquo;`) so it reads as intentional, not
  as a markup error. The irony must survive in `<title>` and
  meta description too. Meta description direction:
  "Session reports our AI assistant writes about working under
  the methodology. Entertaining, biased, occasionally
  insightful."
- The index page opens with a personal note from the author, in
  this spirit (final wording is the human's call):

  > I have a lot of fun, every now and then, asking the AI
  > assistant for a "testimonial" on our work together. You can
  > read some of them here. Take the genre with a grain of salt —
  > an AI reviewing the methodology it was asked to follow is
  > hardly an independent witness — but the incidents they
  > describe are real, and some of the lessons ended up in the
  > framework.

  Note the calibration: the *genre* is disarmed, the *content*
  is not. The incidents in the testimonials are real and dated;
  the warning should not invite the reader to discount them.

## Curation (publish selectively, not all four)

Source files: `drafts/testimonials/TESTIMONIAL_BY_AI*.md`.

Suggested order of value:
1. **TESTIMONIAL_BY_AI_2.md** (build/break/harden sessions) and
   **TESTIMONIAL_BY_AI_4.md** (website build) — the strongest:
   dense in concrete incidents, including failures ("I break the
   rules when they are not in the chain"; the extraction
   subagent that silently produced wrong output under a correct
   chain hash).
2. **TESTIMONIAL_BY_AI_3.md** (v2→v3 migration + service merge)
   — good, publish second wave.
3. **TESTIMONIAL_BY_AI.md** (the first, most philosophical) —
   largely overlaps the published Rationale; maybe never.

Confirm the selection with the human before wiring.

## Editorial pass before publishing

- The human does not endorse the token-count arithmetic that
  appears in some testimonials (e.g., per-update token costs).
  Either remove those numbers or keep them with an explicit
  "values observed in that session, not benchmarks" framing.
  Ask the human per case.
- Keep each testimonial dated and model-attributed (they already
  are: model name + date range in the subtitle). That metadata
  is part of what makes them legible as records.
- Otherwise edit lightly — the first-person voice, including the
  uncomfortable admissions, is the value. Do not sand it down.

## Technical wiring

Follow the existing articles pipeline in the site's spec tree —
mirror how `/articles` works:

- Content side: one node per testimonial under
  `code-from-spec/content/sections/` (sibling of `articles/`),
  each with a `content.input.md` (frontmatter: title,
  description, date) referenced via `external`, generating an
  `output.md`. Plus an index node with the author's opening
  note.
- HTML side: corresponding nodes under `code-from-spec/html/sections/`.
- Navigation: add the section to the header nav (it appears in
  the default/article templates — find where "Articles" is
  declared in the design/template specs and add the sibling).
  Nav label: AI "Testimonials" (curly quotes).
- Regenerate via the normal orchestration flow (`validate_specs`
  → stale → generate), which will also update nav across pages.

## Context worth knowing

- These testimonials double as the site's editorial quarry: the
  confinement and context-management articles (June 2026) were
  mined from them and from `drafts/RATIONALE.md`. Publishing
  does not change that role; `drafts/` remains the working
  copy / source of any future ones.
- TESTIMONIAL_BY_AI_4.md contains a counterpoint to the
  confinement article's closing ("trust the hash"): a correct
  chain hash over silently wrong output. If the testimonials
  get published, that tension becomes visible to readers — it
  is fine (it matches the site's honesty about open problems),
  and it is also a candidate topic for a future article on
  failure modes.
