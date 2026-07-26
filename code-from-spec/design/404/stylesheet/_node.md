---
depends_on:
  - SPEC/design/default/template
  - ARTIFACT/design/default/stylesheet
output: public/css/404.css
---

# SPEC/design/404/stylesheet

Theme stylesheet for the 404 error page. Loaded after
baseline.css, instead of default.css.

# Agent

Generate a CSS stylesheet for the 404 page.

The header and footer must look identical to every other
page on the site. To achieve this, copy every rule from
the default stylesheet (provided as a dependency) that
applies to `.container`, `header`, `footer`, `.site-name`,
`.logo`, `header nav`, and the responsive media query for
those elements. Copy them exactly, do not rewrite or
reinterpret them.

Then add the following rules specific to the 404 page.
These must not conflict with the copied rules above.

1. **Main** — `display: flex`, `flex-direction: column`,
   `align-items: center`, `justify-content: center`,
   `min-height: calc(100vh - 200px)`.

2. **Main container** — `main .container` gets
   `width: 100%` and `text-align: center`. It inherits
   `max-width` and padding from the global `.container`
   rule copied above.

3. **Image** — `main img` gets `max-width: 480px`,
   `width: 100%`, `height: auto`, `position: static`,
   `left: auto`, `transform: none`. The position/left/
   transform overrides cancel the default theme's
   breakout behavior so the image stays centered inside
   the container.

4. **Heading (h1)** — `main h1` gets `margin-top: 1.5em`,
   `font-size: 1.5em`, `text-transform: uppercase`,
   `letter-spacing: 0.1em`, `color: var(--color-muted)`.

5. **Link** — `main a` gets `display: inline-block`,
   `margin-top: 1em`, `color: var(--color-accent)`,
   `text-decoration: none`. On hover, `text-decoration:
   underline`.

6. **Responsive** — inside the existing `@media
   (max-width: 768px)` block (from the copied rules),
   add: `main img { max-width: 320px; }`.

Place the artifact tag as a CSS comment on the first
line.
