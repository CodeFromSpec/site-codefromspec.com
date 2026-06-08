---
depends_on:
  - ROOT/design/default/template
output: public/blog/index.html
---

# ROOT/html/sections/blog/index

The blog index page. Lists all articles in reverse
chronological order (newest first). This page does not
consume a content artifact — it is generated directly
from the article list below.

# Agent

Generate the blog index page HTML file. Use the HTML
layout template exactly as prescribed.

The page title (h1) is: "Blog"

Introduction paragraph: "Notes on building with Code
from Spec — progress reports, lessons learned,
roadblocks, and future directions."

Article list — each entry uses this structure:

```html
<article>
  <h2><a href="{path}">{title}</a></h2>
  <time datetime="{YYYY-MM-DD}">{Month DD, YYYY}</time>
  <p>{description}</p>
</article>
```

Articles (newest first):

- Title: "Context management by construction"
  Path: /blog/context-management
  Date: 2026-06-08
  Description: How Code from Spec gives AI agents
  exactly the context they need — no more, no less.
