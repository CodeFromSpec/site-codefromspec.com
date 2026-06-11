# ROOT/design

Visual design for the site.

# Public

## Shared design values

Values shared across all themes:

- Body font: `-apple-system, BlinkMacSystemFont, "Segoe UI",
  Roboto, Oxygen, Ubuntu, Cantarell, sans-serif`
- Code font: `"SF Mono", "Fira Code", "Fira Mono",
  "Roboto Mono", Consolas, monospace`
- Max content width: 720px
- Base font size: 18px, line height 1.6
- Heading sizes: h1 2em, h2 1.5em, h3 1.25em
- Heading line-height: 1.3

## Site identity

- Site name: "Code from Spec"
- Domain: codefromspec.com
- Contact: gustavo@codefromspec.com
- License: Creative Commons Attribution 4.0 International
- GitHub: https://github.com/CodeFromSpec

## Navigation

Header links:
- Rationale → /rationale
- Articles → /articles
- GitHub → https://github.com/CodeFromSpec (external)

Footer (no navigation, just meta):
- Contact: gustavo@codefromspec.com
- Licensed under CC BY 4.0 → /license

# Decisions

## Three axes of design

The design is organized along three axes: fonts,
colors, and layout. These are the primitive dimensions
that define a theme. Spacing is part of layout, not a
separate axis.

## Baseline + theme model

A baseline CSS defines element-level styles (reset,
typography, links, code, images, blockquotes) using
CSS custom properties for colors. Themes override the
variables and add layout-specific styles (header,
footer, container, responsive).

This allows theming by swapping CSS files without
changing HTML. The baseline is shared; the theme is
the variable.

## Footer has no navigation

The header already links all pages. Repeating
navigation in the footer is redundant for a site with
few pages. The footer shows only contact and license.

Considered: footer with full nav (common web pattern).
Discarded: redundant for this site's scale.

## Site identity and navigation live here temporarily

Site identity and navigation are not design — they are
site-level concerns. They belong in ROOT or a dedicated
intermediate. They are here because ROOT has no
'Public' section by design choice. This should be
revisited.
