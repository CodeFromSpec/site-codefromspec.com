---
depends_on:
  - ROOT/design(HTML layout template)
input: ARTIFACT/content/landing
output: public/index.html
---

# ROOT/html/home

Transforms the landing page content into a complete
HTML page.

# Agent

Generate the home page HTML file following the rendering
process described above. The content to transform is
provided as input.

Special case for the home page: the `<title>` is just
"Code from Spec" (no suffix).
