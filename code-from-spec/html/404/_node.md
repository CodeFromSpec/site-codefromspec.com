---
depends_on:
  - SPEC/design/default/template
  - ARTIFACT/design/404/stylesheet
input: EXTERNAL/code-from-spec/html/404/content.md
output: public/404.html
---

# SPEC/html/404

# Agent

Add `<meta name="robots" content="noindex">` to the `<head>`.
This page should not be indexed by search engines.

Do not include `og:image` or `og:url` meta tags. This is an
error page, not content.

Replace the default theme stylesheet (`/css/default.css`) with
the 404 theme stylesheet (`/css/404.css`) in the `<link>` tag.
