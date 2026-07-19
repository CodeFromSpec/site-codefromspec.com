---
depends_on:
  - SPEC/design/theory/template
output: public/css/theory.css
---

# SPEC/design/theory/stylesheet

Theme stylesheet for theory entries. Works alongside
baseline.css. Magazine-style layout where images float
alongside text.

# Agent

Generate a theme CSS stylesheet. This file is loaded
after baseline.css and adds layout-specific styles for
the theory theme.

Since this theme uses the base color defaults, do not
redefine CSS custom properties.

The stylesheet must cover:

1. **Container** — `.container` at max-width (from
   design values), centered with auto margins, 24px
   horizontal padding.

2. **Header** — bottom border using `var(--color-border)`,
   16px vertical padding. Container uses flexbox: site
   name on the left, nav on the right, vertically
   centered. `.site-name` is bold, text color, no
   underline on hover. `.logo` has height 36px, width
   auto, display inline, vertical-align middle. Nav
   links have 24px gap.

3. **Footer** — top border using `var(--color-border)`,
   32px vertical padding, 0.875em font size, muted
   color. Container is flex with justify-content
   space-between, wrap.

4. **Main** — 20px vertical padding.

5. **Images** — all images: max-width 40% of the
   container, border-radius 4px. Images directly in the
   container (outside sections, e.g. after the h1):
   float right, margin: 0 0 1em 1.5em. Images inside
   sections alternate float direction: odd sections
   (`section:nth-of-type(odd) img`): float right,
   margin: 0 0 1em 1.5em. Even sections
   (`section:nth-of-type(even) img`): float left,
   margin: 0 1.5em 1em 0.

6. **Clearfix** — sections clear floats:
   `section::after` with `content: ""`, `display: table`,
   `clear: both`. This prevents floated images from
   bleeding into the next section.

7. **Navigation** — `main .container > p:last-child`
   (a direct child `<p>` of the container, and the last
   child) is centered (`text-align: center`), muted
   color, margin-top 2em.

8. **Responsive** — at max-width 768px: container
   padding 16px, header stacks vertically
   (flex-direction column, align flex-start, 12px gap),
   nav wraps (16px gap), main padding 20px 0.
   Images stop floating: `float: none`,
   `max-width: 100%`, `margin: 0 auto 1em auto`,
   `display: block` (stacked layout on mobile).

Place the artifact tag as a CSS comment on the first
line.
