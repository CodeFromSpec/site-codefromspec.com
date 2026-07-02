# AGENTS.md

## Publishing a journal entry

Journal entries live under `code-from-spec/html/sections/journal/entries/`.
Each entry is a leaf node with a `content.md` alongside its `_node.md`.

### Steps

1. Create the node directory under
   `code-from-spec/html/sections/journal/entries/<slug>/`.

2. Create `_node.md` following the pattern of existing entries:

   ```yaml
   ---
   depends_on:
     - SPEC/design/journal/template
   input: EXTERNAL/code-from-spec/html/sections/journal/entries/<slug>/content.md
   output: public/journal/<slug>/index.html
   ---

   # SPEC/html/sections/journal/entries/<slug>
   ```

3. Create `content.md` with frontmatter and body:

   ```markdown
   ---
   title: <Entry title>
   description: "<One-line summary for meta and journal index>"
   date: <YYYY-MM-DD>
   ---

   ![<Entry title>](/images/journal/<image-file>)

   # <Entry title>

   <Month Day, Year>

   <body>
   ```

4. Add the entry to the index at
   `code-from-spec/html/sections/journal/index/content.md`.
   Insert in chronological order (newest first). Format:

   ```
   - [Title](/journal/<slug>) — <Month Day, Year> — <description>
   ```

5. Run `/cfs-status` to confirm staleness. Expect at least
   the new entry (missing) and the index (stale).

6. Generate with `/cfs-generate`.
