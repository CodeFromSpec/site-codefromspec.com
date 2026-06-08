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
