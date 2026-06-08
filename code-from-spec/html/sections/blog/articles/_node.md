# ROOT/html/sections/blog/articles

HTML rendering for individual blog articles. Each leaf
node transforms one article from the content layer into
a complete HTML page.

# Public

## Blog article rendering

Blog articles have a `date` field in their content
frontmatter. The content body already includes the
`<h1>` and `<time>` elements — insert the body verbatim
following the rendering process from ROOT/html.
