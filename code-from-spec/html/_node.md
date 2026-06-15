# SPEC/html

Transforms content files into final HTML pages using
the design system — similar to how static site
generators (Hugo, Jekyll, Astro) work. Each page is
generated from a corresponding content file.

# Public

## Rendering process

1. Parse the content file's YAML frontmatter to extract
   `title`, `description`, and `date` (if present).
2. Start from the HTML layout template received via
   `depends_on`. Fill the placeholders:
   - `{artifact-tag}` — the artifact tag for this page.
   - `{Page Title}` — the `title` from frontmatter.
     Use the suffix pattern prescribed by the template.
   - `{page-specific description}` — the `description`
     from frontmatter.
   - `{page content here}` — the markdown body converted
     to semantic HTML. Insert verbatim — do not add
     any elements automatically.

## Markdown to HTML conversion rules

- `#` heading → `<h1>`.
- `##` headings → a section containing an `<h2>` and
  the section content up to the next `##`. Each `##`
  starts a new section.
- Paragraphs → `<p>`.
- `- ` bullet lists → `<ul>` with `<li>`.
- `1. ` numbered lists → `<ol>` with `<li>`.
- `` `code` `` → `<code>`.
- Fenced code blocks → `<pre><code>`.
- `**bold**` → `<strong>`.
- `[text](url)` → `<a href="url">`. Links to external
  URLs (starting with `http`) get
  `target="_blank" rel="noopener"`.
- `> quote` → `<blockquote><p>`.
- `![alt](path)` → `<img src="path" alt="alt">`.
- If the frontmatter contains `image`, add an
  `og:image` meta tag to the `<head>`.

# Private

## Decisions

## Rendering process does not assume template structure

The rendering process describes what to do in terms of
placeholders (`{Page Title}`, `{page content here}`),
not in terms of HTML elements (`<main>`, `<div>`,
`<h1>`). The template defines the structure; the
rendering process fills it.

Considered: prescribing exact HTML placement (e.g.,
"inside `<main><div class='container'>`"). Discarded:
couples the rendering process to a specific template.
A different theme with a different structure would
break the instructions.

## Body is inserted verbatim

The rendering process does not auto-insert any elements
— no heading, no date, nothing. The converted markdown
body is the complete page content. The author controls
order and presence of all visible elements.

Considered: auto-inserting `<h1>` from title and
`<time>` from date before the body. Discarded: the
author wanted a banner image before the title, which
auto-insertion prevented.
