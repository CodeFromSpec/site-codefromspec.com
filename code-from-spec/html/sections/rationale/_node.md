---
depends_on:
  - ROOT/design(HTML layout template)
input: ARTIFACT/content/sections/rationale
output: public/rationale/index.html
---

# ROOT/html/sections/rationale

Transforms the rationale content into a complete HTML
page.

# Agent

Generate the rationale page HTML file following the
rendering process described above. The content to
transform is provided as input.

Add `class="rationale"` to the `<main>` element for
rationale-specific styling (generous spacing between
sections).
