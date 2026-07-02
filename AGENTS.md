# AGENTS.md

## Publishing an article

Articles live under `code-from-spec/html/sections/articles/articles/`.
Each article is a leaf node with a `content.md` alongside its `_node.md`.

### Steps

1. Create the node directory under
   `code-from-spec/html/sections/articles/articles/<slug>/`.

2. Create `_node.md` following the pattern of existing articles:

   ```yaml
   ---
   depends_on:
     - SPEC/design/article/template
   input: EXTERNAL/code-from-spec/html/sections/articles/articles/<slug>/content.md
   output: public/articles/<slug>/index.html
   ---

   # SPEC/html/sections/articles/articles/<slug>
   ```

3. Create `content.md` with frontmatter and body:

   ```markdown
   ---
   title: <Article title>
   description: "<One-line summary for meta and article index>"
   date: <YYYY-MM-DD>
   ---

   ![<Article title>](/images/articles/<image-file>)

   # <Article title>

   <Month Day, Year>

   <body>
   ```

4. Add the article to the index at
   `code-from-spec/html/sections/articles/index/content.md`.
   Insert in chronological order (newest first). Format:

   ```
   - [Title](/articles/<slug>) — <Month Day, Year> — <description>
   ```

5. Run `/cfs-status` to confirm staleness. Expect at least
   the new article (missing) and the index (stale).

6. Generate with `/cfs-generate`.
