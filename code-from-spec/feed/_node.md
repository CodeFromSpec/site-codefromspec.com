---
input: EXTERNAL/code-from-spec/html/sections/journal/index/content.md
output: public/feed.xml
---

# SPEC/feed

The RSS feed for the journal, served at
`https://codefromspec.com/feed.xml`. Generated from the
journal index content file, so publishing a new entry
(which always updates the index) automatically makes the
feed stale.

This node lives outside the `html` tree on purpose: it
produces XML, and inheriting the HTML rendering rules
would contaminate its chain.

# Agent

Generate an RSS 2.0 document. The input is the journal
index content file. Each bullet in its list has the form:

```
- [Title](/journal/<slug>) — <Month Day, Year> — <description>
```

Produce one `<item>` per bullet, preserving order
(newest first).

## Document structure

- Line 1: `<?xml version="1.0" encoding="UTF-8"?>`
- Line 2: `<!-- code-from-spec: {artifact-tag} -->` where
  `{artifact-tag}` is this node's artifact tag.
- Root element:
  `<rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom">`
  containing a single `<channel>`.

## Channel

- `<title>` — `Code from Spec — Journal`
- `<link>` — `https://codefromspec.com/journal/`
- `<atom:link href="https://codefromspec.com/feed.xml" rel="self" type="application/rss+xml"/>`
- `<description>` — the paragraph text from the input
  body (the sentence following the `# Journal` heading).
- `<language>` — `en`
- `<lastBuildDate>` — same value as the newest item's
  `<pubDate>`.

## Items

One `<item>` per bullet, with:

- `<title>` — the bullet's link text.
- `<link>` — the bullet's URL made absolute with a
  trailing slash:
  `https://codefromspec.com/journal/<slug>/`.
- `<guid isPermaLink="true">` — same value as `<link>`.
- `<pubDate>` — the bullet's date in RFC 822 format with
  the optional day-of-week omitted, midnight GMT:
  `19 Jul 2026 00:00:00 GMT`.
- `<description>` — the bullet's description text.

## Escaping

Escape `&`, `<`, and `>` in text content. Typographic
characters (em dashes, curly quotes) stay as-is — the
document is UTF-8.

# Private

## Decisions

### Summary feed generated from the journal index

The feed's only input is the journal index content file;
items carry title, link, date, and description.

Considered: a full-content feed with each entry's
`content.md` in `depends_on`. Discarded: it duplicates
the html layer's markdown-to-HTML conversion inside the
feed, and every new entry would require editing this
node's frontmatter. With the index as input, the
existing publishing workflow (which always updates the
index) makes the feed stale automatically — zero extra
maintenance.

### Root node outside the html tree

`SPEC/html`'s public section prescribes HTML template
rendering and markdown-to-HTML rules that do not apply
to XML. Placing this node under `html/` would deliver
those instructions into its chain.

### Feed lives at /feed.xml

Considered: `/journal/feed.xml`. Discarded: the journal
is the site's only chronological content, and the root
path is the conventional, discoverable location.

### Day-of-week omitted from pubDate

RFC 822 makes the weekday optional. Omitting it removes
a class of generation errors (wrong weekday for a given
date) that no feed reader would compensate for.
