# SPEC/design/theory/template

# Public

## HTML layout template

Every page is a standalone HTML file. The following
structure must be reproduced exactly — only the marked
parts vary.

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
  <meta property="og:title" content="{Page Title} — Code from Spec">
  <meta property="og:description" content="{page-specific description}">
  <meta property="og:image" content="https://codefromspec.com{page-image-path}">
  <meta property="og:url" content="{canonical-url}">
  <meta property="og:type" content="article">
  <meta property="og:site_name" content="Code from Spec">
  <meta name="twitter:card" content="summary_large_image">
  <link rel="apple-touch-icon" sizes="180x180" href="/images/favicon/apple-touch-icon.png">
  <link rel="icon" type="image/png" sizes="32x32" href="/images/favicon/favicon-32x32.png">
  <link rel="icon" type="image/png" sizes="16x16" href="/images/favicon/favicon-16x16.png">
  <link rel="manifest" href="/images/favicon/site.webmanifest">
  <link rel="stylesheet" href="/css/baseline.css">
  <link rel="stylesheet" href="/css/theory.css">
</head>
<body>
  <header>
    <div class="container">
      <a href="/" class="site-name"><img src="/images/code_from_spec_logo.png" alt="" class="logo"> Code from Spec</a>
      <nav>
        <a href="/rationale">Rationale</a>
        <a href="/theory">Theory</a>
        <a href="/journal">Journal</a>
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
      <span><a href="mailto:gustavo@codefromspec.com">gustavo@codefromspec.com</a></span>
      <span>Licensed under <a href="/license">CC BY 4.0</a></span>
    </div>
  </footer>
</body>
</html>
```

Variable parts:

- `{artifact-tag}` — the artifact tag, unique per page
- `{page-specific description}` — meta description
- `{Page Title}` — "{Name} — Code from Spec"
- `{page content here}` — the page's main content
- `{page-image-path}` — the path of the first image in
  the content (e.g. `/images/theory/20260718_image_part_I.png`).
  Combined with `https://codefromspec.com` to form the
  absolute OG image URL.
- `{canonical-url}` — the full canonical URL of the page
  (e.g. `https://codefromspec.com/theory/part-i/`).
  Use a trailing slash.

Use `rel="noopener"` on external links. Do not add
`noreferrer`.
