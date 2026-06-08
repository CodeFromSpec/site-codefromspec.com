# ROOT/html

Transforms content files into final HTML pages using
the design system — similar to how static site
generators (Hugo, Jekyll, Astro) work. Each page is
generated from a corresponding content file.

# Public

## Rendering process

1. Parse the content file's YAML frontmatter to extract
   `title`, `description`, and `date` (if present).
2. Use the HTML layout template from ROOT/design.
3. Set `<title>` to `{title} — Code from Spec`
   (except the home page: just `Code from Spec`).
4. Set `<meta name="description">` to `{description}`.
5. Inside `<main><div class="container">`, place:
   a. `<h1>{title}</h1>`
   b. If `date` is present:
      `<time datetime="{YYYY-MM-DD}">{Month D, YYYY}</time>`
   c. The markdown body converted to semantic HTML.
6. The header, footer, and all boilerplate come from the
   template — do not modify them.

## Markdown to HTML conversion rules

- `##` headings → `<section>` containing `<h2>` and the
  section content up to the next `##`. Each `##` starts
  a new `<section>`.
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
- If the frontmatter contains `image`, add
  `<meta property="og:image" content="{image}">` to
  the `<head>`.
