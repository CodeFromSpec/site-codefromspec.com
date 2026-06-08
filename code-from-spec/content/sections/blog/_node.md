# ROOT/content/sections/blog

Extraction nodes for blog article content files.

# Public

## Article frontmatter

Blog articles include a `date` field in the frontmatter:

```
---
title: {article title}
description: {one-sentence description}
date: {YYYY-MM-DD}
---
```

## File naming

Article source files are named `{YYYYMMDD}_{slug}.md`
where the date prefix matches the `date` frontmatter
field. This ensures chronological ordering in the
filesystem.
