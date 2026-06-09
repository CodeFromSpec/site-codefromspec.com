---
depends_on:
  - ROOT/design/default/template
output: public/css/default.css
---

# ROOT/design/default/stylesheet

Theme stylesheet for the default (light) theme. Works
alongside base.css. Defines layout and structure —
the base handles typography and element styles.

# Agent

Generate a theme CSS stylesheet. This file is loaded
after base.css and adds layout-specific styles for
the default theme.

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
   underline on hover. Nav links have 24px gap.

3. **Footer** — top border using `var(--color-border)`,
   32px vertical padding, 0.875em font size, muted
   color. Container is flex with 16px gap, wrap.

4. **Main** — 20px vertical padding.

5. **Subheading** — `h1 + p` styled at 1.25em, muted
   color.

6. **Rationale page** — `.rationale h2` has margin-top
   2.5em for generous spacing between sections.

7. **Article elements** — `article` has 2em bottom
   margin. `article h2 a` is 1.1em bold, accent color,
   block display. `article time` is block, 0.875em,
   muted color.

8. **Blog article date** — `main time` (date after h1)
   is block, 0.875em, muted color, margin-bottom 1.5em.

9. **Images** — break out of the container and center
   on the viewport regardless of size. Use
   `position: relative`, `left: 50%`,
   `transform: translateX(-50%)`, `max-width: 100vw`.
   No vertical margin.

10. **Responsive** — at max-width 768px: container
   padding 16px, header stacks vertically
   (flex-direction column, align flex-start, 12px gap),
   nav wraps (16px gap), main padding 20px 0.

Place the artifact tag as a CSS comment on the first
line.
