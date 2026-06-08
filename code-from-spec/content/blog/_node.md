# ROOT/content/blog

Articles about Code from Spec — progress reports,
lessons learned, roadblocks, and future directions.

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

Article files are named `{YYYYMMDD}_{slug}.md` where
the date prefix matches the `date` frontmatter field.
This ensures chronological ordering in the filesystem.
