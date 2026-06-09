# ROOT/design/default

Light theme. Header with navigation on the right,
single column layout, light background.

# Public

## Theme colors

Uses the base CSS custom property defaults — no
overrides needed. Light background, dark text, blue
accent.

# Decisions

## Images break out of the container

In the default theme, images use
`position: relative; left: 50%; transform: translateX(-50%)`
to center on the viewport regardless of size. Smaller
images stay at their natural size; larger images are
limited to viewport width.

Considered: keeping images inside the 720px container.
Discarded: banners with white backgrounds blend with the
page and benefit from breathing room beyond the text
column.

## Background color is #fefdfe, not white

The banner images have a near-white background (#fefdfe).
Using pure white (#ffffff) for the page background made
the image borders visible. Matching the page background
to the image eliminates the seam.
