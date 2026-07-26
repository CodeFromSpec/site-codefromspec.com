# site-codefromspec.com

The Code from Spec project website — built with Code from Spec.

## Why Code from Spec for a static site?

It isn't the ideal tool for this. A static site generator like
Hugo or Astro would be simpler and more appropriate. This site
was built with Code from Spec as a practical experiment — to
test the methodology on a real project, learn from the friction,
and discover what works and what needs improvement. Several
insights about the framework came directly from this exercise.

## Structure

The site uses three layers, inspired by static site generators:

- **design/** — visual design (baseline CSS, themes with templates and stylesheets)
- **html/** — transforms content into HTML using design templates

Content files (markdown with frontmatter) live alongside their spec nodes in the `code-from-spec/` tree.
Generated HTML is output to `public/`, which is the deploy directory.

## Pages

| URL | Description |
|---|---|
| `/` | Landing page — overview of the methodology |
| `/rationale` | Full rationale — why Code from Spec exists (frozen, April 2026) |
| `/theory` | Theory essay index — Parts I–VI and conclusion under `/theory/part-*` and `/theory/conclusion` |
| `/journal` | Journal index |
| `/journal/context-management` | Journal: Context management by construction |
| `/journal/confinement` | Journal: Confinement |
| `/journal/anchoring-on-old-code` | Journal: Anchoring on old code |
| `/journal/telling-the-agent-what-changed` | Journal: Telling the agent what changed |
| `/journal/growing-pains` | Journal: Growing pains |
| `/journal/could-we-stop-reviewing-code` | Journal: Could we stop reviewing code? |
| `/license` | CC BY 4.0 license |
| `/feed.xml` | RSS feed of the journal |

## Hosting

Cloudflare Pages. Push to `main` triggers deployment from the `public/` directory.

## License

Creative Commons Attribution 4.0 International.
See [LICENSE](LICENSE) for details.
