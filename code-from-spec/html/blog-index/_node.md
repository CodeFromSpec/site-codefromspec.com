---
depends_on:
  - ROOT/design(HTML layout template)
output: public/blog/index.html
---

# ROOT/html/blog-index

The blog index page. Lists all articles in reverse
chronological order (newest first).

No articles exist yet.

# Agent

Generate the blog index page HTML file.

1. Use the HTML layout template exactly as prescribed.
2. The page title (h1) is: "Blog"
3. Introduction paragraph: "Notes on building with Code
   from Spec — progress reports, lessons learned,
   roadblocks, and future directions."
4. Below the introduction: "No articles yet. Check back
   soon."
5. When articles exist in the future, each entry will
   use this structure:

   ```html
   <article>
     <h2><a href="/blog/{slug}" class="article-title">{title}</a></h2>
     <time datetime="{YYYY-MM-DD}" class="article-date">{Month DD, YYYY}</time>
     <p>{brief description}</p>
   </article>
   ```
