# SPEC/design/journal

Magazine-style layout where images float alongside
text instead of breaking out of the container.

# Private

## Decisions

## Separate theme for the journal

Journal entries need images that float alongside text
(magazine style), not the full-bleed behavior of the
default theme. A separate theme allows each html node
to choose its layout via `depends_on`.

Considered: a single theme with conditional CSS.
Discarded: the image behavior is fundamentally
different — float vs. full-bleed. Two themes are
cleaner than one theme with exceptions.

## Images float right at 40% width

Images float right and text wraps around them,
limited to 40% of the container width. On mobile
(below 768px), float is removed and images stack
in a single column.

Considered: 45% width. Discarded: 40% gives more
room for text to remain readable alongside the image.
