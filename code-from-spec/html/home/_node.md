---
depends_on:
  - ROOT/design(HTML layout template)
external:
  - path: content/landing.md
output: public/index.html
---

# ROOT/html/home

Transforms the landing page content into a complete
HTML page.

# Agent

Generate the home page HTML file following the rendering
process in the inherited context. The content file is
provided via external.

Special case for the home page: the `<title>` is just
"Code from Spec" (no suffix).
