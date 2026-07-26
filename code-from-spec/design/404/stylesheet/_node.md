---
depends_on:
  - SPEC/design/default/template
output: public/css/404.css
---

# SPEC/design/404/stylesheet

Theme stylesheet for the 404 error page. Loaded after
baseline.css.

# Agent

Generate a CSS stylesheet for the 404 page.

The page content is a single image, a heading, and a
link, inside the standard template's `<main>` container.
The goal is to center everything vertically and
horizontally in the viewport (minus the header and
footer).

Requirements:

1. **Main** — uses flexbox to center its container
   vertically in the available space. Set `min-height`
   to fill the viewport minus header and footer (use
   `calc(100vh - 200px)` as a reasonable estimate).
   `flex-direction: column`, `align-items: center`,
   `justify-content: center`.

2. **Container** — `text-align: center`.

3. **Image** — `max-width: 480px`, `width: 100%`,
   `height: auto`. No breakout (unlike the default
   theme, the image stays inside the container).

4. **Heading (h1)** — `margin-top: 1.5em`,
   `font-size: 1.5em`, uppercase, letter-spacing 0.1em,
   color `var(--color-muted)`.

5. **Link** — `display: inline-block`,
   `margin-top: 1em`, accent color, no underline,
   underline on hover.

6. **Header/footer** — same as the default theme
   (bottom/top border, flexbox layout, site name, nav).
   Copy the relevant rules from the default stylesheet
   spec so the page is self-contained.

7. **Responsive** — at max-width 768px: image
   `max-width: 320px`, container padding 16px, header
   stacks vertically.

Place the artifact tag as a CSS comment on the first
line.
