# ROOT/html

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
   - `{page content here}` — the page heading, optional
     date, and the converted markdown body (see below).
3. The page content placed in `{page content here}` is
   assembled as:
   a. A visible heading with the `title` from frontmatter.
   b. If `date` is present, the formatted date
      (Month D, YYYY).
   c. The markdown body converted to semantic HTML.

## Markdown to HTML conversion rules

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
