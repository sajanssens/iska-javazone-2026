# AGENTS.md

This repo is a `kc-cli` slide deck for a weekly internal knowledge session:
**JavaZone 2026 Oslo: Key Takeaways**, handpicked by Bram.

## Project structure

- `slides/` — one folder per module, each containing numbered `.md` slide files.
  - `00-intro/` — opening title slide and agenda.
  - `01-canary-rollback-ai/` through `06-docker-and-nix/` — one module per JavaZone
    topic, each currently scaffolded with a title slide (`01-title.md`) and a
    placeholder content slide (`02-content.md`) still marked `TODO`.
  - `99-wrap-up/` — recap and closing slide.
- `css/custom.css` — project-specific styling overrides.
- `img/` — images used by slides.
- `kc.json` — kc-cli config.

## Slide format

Slides are plain Markdown parsed by `kc-cli` (Reveal.js + Markdown plugin). Key
conventions used in this repo:

- Multiple slides per file are split with `---` on its own line, surrounded by
  blank lines.
- Slide-level styling is added via an HTML comment right after the heading:
  `<!-- .slide: class="is-module" -->`. Common classes: `is-welcome` (deck
  opening title), `is-module` (module/chapter title), `is-fancy1`/`is-fancy2`/
  `is-fancy3` (emphasis), `is-lab`, `is-closing` (final slide), `is-empty`
  (disable theming).
- Element-level styling uses `<!-- .element: class="..." -->` directly after
  the element.
- Speaker notes go under a `Notes:` line at the end of a slide's content.
- Keep one message per slide, short phrases over full sentences for slides
  presented live, and roughly six objects max per slide.
- Keep slide files short (rough ceiling of ~200 lines); split into another
  numbered file in the same module folder instead of letting one file grow.

## Working on this deck

- When filling in placeholder content slides, replace the `TODO` line with
  real bullets/diagrams/code as appropriate, following the conventions above.
- Don't restructure existing module/file breakdown unless asked; slide content
  work should fill in placeholders, not reorganize the deck.
- Use the `slide-designer` skill (if available in the agent environment) for
  guidance on wording style, layout classes, theming classes, and components.
