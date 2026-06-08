# ROOT/html

Transforms content artifacts into HTML pages using the
design system. Each leaf node consumes a content artifact
via `input` (or generates standalone content) and
produces an HTML file.

# Public

## Rendering conventions

- Use the HTML layout template from ROOT/design exactly.
- Use semantic HTML: `<section>`, `<article>`, `<nav>`,
  `<header>`, `<footer>`, `<h1>`–`<h3>`.
- Convert markdown headings (`##`) to `<h2>` within
  `<section>` elements.
- Convert markdown paragraphs to `<p>` elements.
- Convert markdown bullet lists to `<ul>` with `<li>`.
- Convert markdown numbered lists to `<ol>` with `<li>`.
- Convert markdown code blocks to `<pre><code>`.
- Convert markdown inline code to `<code>`.
- Convert markdown bold (`**text**`) to `<strong>`.
- Convert markdown links to `<a>`. External links get
  `target="_blank" rel="noopener"`.
