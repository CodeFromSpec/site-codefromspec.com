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

- **content/** — markdown files with frontmatter, written manually
- **design/** — visual design (baseline CSS, themes with templates and stylesheets)
- **html/** — transforms content into HTML using design templates

Content files live alongside their spec nodes in the `code-from-spec/` tree.
Generated HTML is output to `public/`, which is the deploy directory.

## Pages

| URL | Description |
|---|---|
| `/` | Landing page — overview of the methodology |
| `/rationale` | Full rationale — why Code from Spec exists |
| `/articles` | Article index |
| `/articles/context-management` | Article: Context management by construction |
| `/license` | CC BY 4.0 license |

## Hosting

Cloudflare Pages. Push to `main` triggers deployment from the `public/` directory.

## License

Creative Commons Attribution 4.0 International.
See [LICENSE](LICENSE) for details.
