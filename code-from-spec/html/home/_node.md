---
depends_on:
  - ROOT/design(HTML layout template)
input: ARTIFACT/content/landing
output: public/index.html
---

# ROOT/html/home

Transforms the landing page content into HTML.

# Agent

Generate the home page HTML file.

1. Read the input artifact (landing page content in
   markdown).
2. Convert it to HTML following the rendering conventions
   in the inherited context.
3. Use the HTML layout template exactly as prescribed.
4. The hero section (heading + subheading + paragraph +
   call-to-action link) goes directly in `<main>` without
   a `<section>` wrapper. Use `<p class="subheading">`
   for the subheading. The CTA is a `<p>` with an `<a>`
   to the framework GitHub repository.
5. Each subsequent section uses `<section>` with an
   `<h2>`.
6. The "Who benefits" subsections use `<h3>` inside the
   parent `<section>`.
7. The "Getting started" steps use `<ol>`.
8. The "Learn more" links use `<ul>`.
