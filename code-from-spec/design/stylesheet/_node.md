---
output: public/css/style.css
---

# ROOT/design/stylesheet

The single CSS stylesheet for the site.

# Agent

Generate a CSS stylesheet using the design values and
responsive behavior from the inherited context.

The stylesheet must cover:

1. **Reset and base** — minimal reset (box-sizing,
   margin/padding on body), base typography.

2. **Layout** — `.container` class at max-width 720px,
   centered, with 24px horizontal padding.

3. **Header** — flexbox with site name on the left,
   nav links on the right. `.site-name` is bold, text
   color, no underline. Nav links use accent color,
   24px gap.

4. **Footer** — top border separator. Two rows: `nav`
   with page links (16px gap, wrapping), then
   `.footer-meta` with contact and license (16px gap,
   wrapping). Muted text color, 0.875em font size.

5. **Typography** — h1: 2em, h2: 1.5em, h3: 1.25em.
   Headings have line-height 1.3, margin-top 1.5em,
   margin-bottom 0.5em. Paragraphs have margin-bottom
   1em.

6. **Links** — accent color, underline on hover only.

7. **Code** — inline `code` uses code font, 0.875em,
   `#f5f5f5` background, 2px 5px padding, 3px radius.
   `pre` blocks use `#f5f5f5` background, 16px padding,
   4px radius, overflow-x auto. `pre code` resets
   background and padding.

8. **Article list** — `.article-list` is an unstyled
   `ul`. Each `li` has 2em bottom margin.
   `.article-title` is a block link, 1.1em bold, accent
   color. `.article-date` is block, 0.875em, muted
   color.

9. **Rationale page** — `.rationale h2` has margin-top
   2.5em. `blockquote` uses left border in accent color,
   4px wide, 8px 16px padding, `#444444` text.

10. **Responsive** — at max-width 768px: body font 16px,
    container padding 16px, header stacks vertically
    (flex-direction column, align flex-start, 12px gap),
    nav wraps (16px gap), h1 1.75em, h2 1.35em,
    h3 1.15em, main padding 24px 0.

Place the artifact tag as a CSS comment on the first
line.
