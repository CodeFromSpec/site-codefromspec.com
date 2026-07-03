---
output: public/css/baseline.css
---

# SPEC/design/baseline

The base stylesheet shared by all themes. Defines
reset, typography, and element styles using CSS custom
properties for theme-specific values.

# Agent

Generate a base CSS stylesheet.

Define CSS custom properties on `:root` with default
(light theme) values:

```
--color-text: #1a1a1a
--color-bg: #fefdfe
--color-accent: #2563eb
--color-muted: #666666
--color-border: #e5e5e5
--color-code-bg: #f5f5f5
--color-blockquote-text: #444444
```

Themes override these variables. The base stylesheet
must use `var(--color-*)` everywhere — never hardcode
color values.

The stylesheet must cover:

1. **Reset** — box-sizing border-box on all elements,
   zero margin/padding on body.

2. **Body** — font family, font size, line height, text
   color, background color from the design values
   described above.

3. **Typography** — heading sizes and line-height from
   the design values. Headings have margin-top 1em,
   margin-bottom 0.5em. Paragraphs have margin-top 0,
   margin-bottom 1em.

4. **Links** — accent color, underline on hover only.

5. **Code** — inline `code` uses code font, 0.875em,
   code-bg background, 2px 5px padding, 3px radius.
   `pre` blocks use code-bg background, 16px padding,
   4px radius, overflow-x auto. `pre code` resets
   background and padding, font-size 0.9em.

6. **Lists** — standard ul/ol with default browser
   styling. li has margin-bottom 0.25em.

7. **Blockquote** — left border 4px in accent color,
   8px 16px padding, blockquote-text color, margin-top 0,
   margin-bottom 1em (so it doesn't collide with the
   element that follows). Last p inside blockquote has
   no bottom margin (the blockquote's own margin-bottom
   provides the spacing instead).

8. **Strong** — font-weight bold (default, just ensure
   it is not overridden).

9. **Images** — `max-width: 100%`, `height: auto`,
   `display: block`.

10. **Responsive** — at max-width 768px: body font 16px,
   h1 1.75em, h2 1.35em, h3 1.15em.

Place the artifact tag as a CSS comment on the first
line.
