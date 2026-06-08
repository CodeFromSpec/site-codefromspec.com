---
external:
  - path: content/landing.md
output: code-from-spec/content/landing/output.md
---

# ROOT/content/landing

Extraction node for the landing page content. Reads the
manually-written content file and produces an artifact
with the artifact tag added to the frontmatter.

# Agent

Copy the external content file verbatim. The only
change is adding the `code-from-spec` field to the
YAML frontmatter.

The output must be identical to the input except for the
artifact tag in the frontmatter.
