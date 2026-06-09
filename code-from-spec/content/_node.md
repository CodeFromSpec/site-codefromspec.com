# ROOT/content

Text content for the site. Content files live in
`content/` at the project root and are written manually
— they are not auto-generated.

# Public

## Extraction instructions

Copy the external content file verbatim. The only
change is adding the `code-from-spec` field as the
first field in the YAML frontmatter. The output must
be identical to the input except for the artifact tag
in the frontmatter.

# Private

## Content file format

Content files are markdown with YAML frontmatter:

```
---
title: {page title}
description: {one-sentence description for meta tag}
---

{markdown content}
```

The frontmatter contains page metadata:

- `title` — used for `<title>` and `<meta>` by the
  html layer. Not rendered visibly — the author
  controls visible headings in the body.
- `description` — used for `<meta name="description">`.
- `date` (optional) — metadata for blog articles. The
  author controls how/whether it appears in the body.

The body is pure markdown. No HTML tags, no CSS classes,
no inline styles. The html layer converts markdown to
semantic HTML.

Additional frontmatter fields may be present depending
on the content type (e.g., `date` for blog articles).

Optional metadata fields:

- `image` — path to an image for social sharing
  (og:image). Relative to the site root
  (e.g., `/images/og-context-management.png`). Image
  files live in `public/images/`.

## Markdown conventions

- `#` for page heading.
- `##` for section headings.
- Normal paragraphs (blank line separated).
- `- ` for bullet lists.
- `1. ` for numbered lists.
- `` `code` `` for inline code.
- Triple backtick fenced blocks for code blocks.
- `**bold**` for emphasis.
- `[text](url)` for links.
- `![alt](path)` for images. Image files live in
  `public/images/`. Paths are relative to the site root
  (e.g., `![Diagram](/images/diagram.png)`).

# Decisions

## Frontmatter is metadata only

The `title` field is used for `<title>` and `<meta>`
tags. It is not automatically rendered as a visible
heading. The author controls everything visible in the
body — including headings, dates, and their order.

Considered: auto-inserting `<h1>` from title and
`<time>` from date (SSG model). Discarded: prevents
the author from placing a banner before the title, or
choosing not to show a heading at all. The author knows
best what should appear on the page.

## Content is purely semantic

No CSS classes, no ids, no inline styles in content
files. All styling is the responsibility of the CSS,
selecting by elements and structure.

Considered: CSS classes in content (e.g.,
`class="subheading"`, `class="article-date"`).
Discarded: mixes content with presentation. Changing
the theme would require editing content files.

## Extraction layer exists for input semantics

Content files are consumed via an extraction layer
that adds the artifact tag, then the html layer uses
`input: ARTIFACT/...` to transform them. This exists
because the framework's `input` field only accepts
artifact references, not raw files.

Considered: using `external` directly in the html
layer. Discarded: `external` means context, `input`
means material to transform. The semantic distinction
matters — converting markdown to HTML is
transformation, not contextualization.
