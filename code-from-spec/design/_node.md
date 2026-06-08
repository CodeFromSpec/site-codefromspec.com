# ROOT/design

Visual design for the site — layout, typography, colors,
and the CSS stylesheet.

# Public

## Site identity

- Site name: "Code from Spec"
- Domain: codefromspec.com
- License: Creative Commons Attribution 4.0 International

## Design values

- Body font: `-apple-system, BlinkMacSystemFont, "Segoe UI",
  Roboto, Oxygen, Ubuntu, Cantarell, sans-serif`
- Code font: `"SF Mono", "Fira Code", "Fira Mono",
  "Roboto Mono", Consolas, monospace`
- Max content width: 720px, centered
- Base font size: 18px, line height 1.6
- Accent color: `#2563eb` (blue) for links and emphasis
- Text color: `#1a1a1a` on `#ffffff` background
- Muted text color: `#666666` (footer, dates)
- Subtle border/separator: `#e5e5e5`
- Code block background: `#f5f5f5`

## Responsive behavior

- Below 768px: reduce horizontal padding, scale font
  to 16px
- Navigation stacks vertically (no hamburger menu)
- Heading sizes reduce proportionally

## HTML layout template

Every page is a standalone HTML file. No templating
engine. The following structure must be reproduced
exactly in every page — only the marked parts vary.

```html
<!DOCTYPE html>
<!-- code-from-spec: {artifact-tag} -->
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="description" content="{page-specific description}">
  <meta name="author" content="Gustavo Silveira Neto">
  <title>{Page Title} — Code from Spec</title>
  <link rel="stylesheet" href="/css/style.css">
</head>
<body>
  <header>
    <div class="container">
      <a href="/" class="site-name">Code from Spec</a>
      <nav>
        <a href="/rationale">Rationale</a>
        <a href="/blog">Blog</a>
        <a href="https://github.com/CodeFromSpec" target="_blank" rel="noopener">GitHub</a>
      </nav>
    </div>
  </header>
  <main>
    <div class="container">
      {page content here}
    </div>
  </main>
  <footer>
    <div class="container">
      <nav>
        <a href="/">Home</a>
        <a href="/rationale">Rationale</a>
        <a href="/blog">Blog</a>
        <a href="/license">License</a>
        <a href="https://github.com/CodeFromSpec" target="_blank" rel="noopener">GitHub</a>
      </nav>
      <div class="footer-meta">
        <span>Contact: <a href="mailto:gustavo@codefromspec.com">gustavo@codefromspec.com</a></span>
        <span>Licensed under <a href="/license">CC BY 4.0</a></span>
      </div>
    </div>
  </footer>
</body>
</html>
```

Variable parts:

- `{artifact-tag}` — the artifact tag, unique per page
- `{page-specific description}` — meta description
- `{Page Title}` — use "Code from Spec" alone for the
  home page; "{Name} — Code from Spec" for others
- `{page content here}` — the page's main content

Use `rel="noopener"` on external links. Do not add
`noreferrer`.
