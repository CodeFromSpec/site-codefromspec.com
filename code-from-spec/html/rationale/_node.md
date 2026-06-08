---
depends_on:
  - ROOT/design(HTML layout template)
input: ARTIFACT/content/rationale
output: public/rationale/index.html
---

# ROOT/html/rationale

Transforms the rationale content into HTML.

# Agent

Generate the rationale page HTML file.

1. Read the input artifact (rationale content in
   markdown).
2. Convert it to HTML following the rendering conventions
   in the inherited context.
3. Use the HTML layout template exactly as prescribed.
4. Add `class="rationale"` to the `<main>` element.
5. The introduction (text before the first `##`) goes in
   a `<blockquote>`.
6. Each `##` section becomes a `<section>` with an
   `<h2>`.
7. Render bullet lists as `<ul>` with `<li>`. Use
   `<strong>` for bold lead-ins.
8. Each blank line in the input separates paragraphs —
   render each as its own `<p>`.
