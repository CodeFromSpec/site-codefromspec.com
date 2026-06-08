---
depends_on:
  - ROOT/design(HTML layout template)
external:
  - path: content/sections/rationale/rationale.md
output: public/rationale/index.html
---

# ROOT/html/sections/rationale

Transforms the rationale content into a complete HTML
page.

# Agent

Generate the rationale page HTML file following the
rendering process in the inherited context. The content
file is provided via external.

Add `class="rationale"` to the `<main>` element for
rationale-specific styling (generous spacing between
sections).
