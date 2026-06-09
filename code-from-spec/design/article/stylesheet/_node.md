---
depends_on:
  - ROOT/design/article/template
output: public/css/article.css
---

# ROOT/design/article/stylesheet

Theme stylesheet for blog articles. Works alongside
baseline.css. Magazine-style layout where images float
alongside text.

# Agent

Generate a theme CSS stylesheet. This file is loaded
after baseline.css and adds layout-specific styles for
the blog theme.

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
   underline on hover. `.site-name img` has height 24px,
   width auto, vertical-align middle. Nav links have
   24px gap.

3. **Footer** — top border using `var(--color-border)`,
   32px vertical padding, 0.875em font size, muted
   color. Container is flex with justify-content
   space-between, wrap.

4. **Main** — 20px vertical padding.

5. **Images** — float right, max-width 40% of the
   container, margin: 0 0 1em 1.5em (no top margin,
   right margin 0, bottom margin 1em, left margin
   1.5em to create space between text and image).
   Border-radius 4px.

6. **Clearfix** — sections clear floats:
   `section::after` with `content: ""`, `display: table`,
   `clear: both`. This prevents floated images from
   bleeding into the next section.

7. **Subheading** — `h1 + p` styled at 1.25em, muted
   color.

8. **Article date** — `main time`, `main p` immediately
   after `h1 + p` (or after `h1` if no subheading):
   0.875em, muted color, margin-bottom 1.5em.

9. **Responsive** — at max-width 768px: container
   padding 16px, header stacks vertically
   (flex-direction column, align flex-start, 12px gap),
   nav wraps (16px gap), main padding 20px 0.
   Images stop floating: `float: none`,
   `max-width: 100%`, `margin: 0 auto 1em auto`,
   `display: block` (stacked layout on mobile).

Place the artifact tag as a CSS comment on the first
line.
