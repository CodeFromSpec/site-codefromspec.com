# SPEC/architecture

The Code from Spec project website — codefromspec.com.

A static site (HTML + CSS, no JavaScript frameworks, no
build step) that presents the Code from Spec methodology
to a technical audience that includes engineering managers
and domain experts, not only programmers.

# Private

## Decisions

## Architecture: three layers

The site uses three layers inspired by static site
generators (Hugo, Jekyll, Astro):

- **content/** — markdown files with frontmatter,
  written manually by humans
- **design/** — visual design (baseline CSS, themes
  with templates and stylesheets)
- **html/** — transforms content into HTML using design

Considered: a single layer generating HTML directly.
Discarded: no separation between content and
presentation. Changing design would require touching
every page spec.

## Content files live alongside their spec nodes

Each content file (`content.input.md`) lives in the
same directory as the `_node.md` that references it.
This keeps everything related to one piece of content
in one folder: the spec, the source file, and the
extraction output.

Considered: a separate `content/` directory at the
project root. Discarded: splits related files across
two locations.
