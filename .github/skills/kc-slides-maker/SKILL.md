---
name: kc-slides-maker
description: 'Create and maintain kc-cli Reveal.js slide decks in Markdown. Use for authoring slides and labs.'
model: 'Claude Sonnet'
---

# kc-cli Slide Maker

Create readable, correctly structured kc-cli/Reveal.js slides in Markdown. Keep each slide focused, use the deck's established conventions, and validate the result against the fixed Reveal stage.

## When to Use

- Create a new kc-cli slide or chapter.
- Edit or split an existing slide.
- Add layouts, fragments, code examples, diagrams, labs, or recap slides.
- Diagnose Markdown, HTML, separator, or slide-overflow problems.

## Procedure

1. Inspect the target deck and nearby slides before editing. Follow the deck's existing placement of slide comments and its established naming conventions.
2. Identify one idea for each slide. Keep the content within the approximately 960 x 700 logical Reveal stage.
3. Use the heading hierarchy, separators, layout classes, and element comments described below.
4. Add fragments when content should be revealed progressively.
5. Validate Markdown and HTML structure, then check for likely overflow and missing recap or lab elements.
6. Render or lint the deck when possible, and step through slides suspected of overflowing.

## Slides and Separators

- A `.md` file may contain multiple slides separated by `---` (three dashes) surrounded by blank lines.
- Two dashes (`--`) are invalid.
- Use `----` (four dashes) for a vertical or nested slide, or to preserve a literal `---`, such as YAML frontmatter inside a code sample.

## Headings

- `#` is the presentation title and should appear once on the welcome slide.
- `##` is a module or chapter title.
- `###` is a topic or slide title.
- `####` is a sub-slide title.

## Slide and Element Classes

Add slide classes with an HTML comment:

```md
<!-- .slide: class="is-welcome" -->
```

The slide comment may appear at the top of a file or after its content. Follow the convention used by the deck.

Corporate identity classes include `is-welcome`, `is-module`, `is-lab`, and `is-closing`. The available decorative corporate identity classes include `is-fancy1`, `is-fancy2`, and `is-fancy3`.

Reveal.js attributes can share the same comment:

```md
<!-- .slide: class="is-module" data-auto-animate -->
```

Add element classes or attributes on the line after the target element, with a blank line before the comment:

```md
Some content

<!-- .element: class="kc-smaller" -->
```

## Layout Classes

Use the built-in classes rather than inventing new inline layout styles:

- `kc-columns` creates equal-width flex columns.
- `kc-flex` creates a flex container without equalizing widths.
- `kc-vertical-center`, `kc-horizontal-center`, `kc-horizontal-spaced`, `kc-horizontal-right`, and `kc-wrap` modify flex alignment or wrapping.
- `kc-grid` creates a 2 x 2 grid. Use `&nbsp;` to skip a cell.
- `kc-gap1` through `kc-gap5` control spacing.
- `kc-smaller` and `kc-smallest` reduce content size when genuinely dense content requires it.
- `kc-left` and `kc-right` float or align content.
- `kc-agenda` formats multi-column lists.
- Tables use `kc-table`, optionally combined with `kc-smaller`, `kc-left`, or `kc-right`.

## Slide Length and Sizing

Reveal renders slides in a fixed approximately 960 x 700 logical box. Content taller than that is clipped rather than scrolled.

The `kc lint` dimensions check runs in a larger headless viewport and is not sufficient to prove that a slide fits on a laptop or projector. A slide can pass lint and still be clipped.

Use this rule of thumb:

- One heading plus 4-6 short bullets is usually safe.
- One heading plus a code block of approximately 10-12 lines is usually safe.
- A full bullet list combined with a full code block commonly overflows.

When a slide is too long:

1. Shorten the content. Remove a bullet or trim a code sample to its essential lines.
2. Split the content into two slides, such as concept followed by example.
3. Use `kc-smaller` or `kc-smallest` only for genuinely dense reference material, not as a substitute for editing.

Render the deck and step through the slide whenever its fit is uncertain.

## Mixing HTML and Markdown

The Markdown parser is sensitive to whitespace when Markdown is wrapped in HTML:

- Leave a blank line after the opening tag and before the closing tag.
- Do not indent Markdown inside the HTML block.
- Add an extra blank line after headings, lists, and code blocks inside the HTML block.
- Close every HTML element.
- Escape stray `<` and `>` characters.

## Code

- Use single backticks for inline code.
- Use fenced code blocks with a language identifier, such as `ts`, `js`, `html`, `css`, `cs`, `java`, `md`, `bash`, or `mermaid`.
- Use four backticks when a code block must contain a literal triple-backtick fence.

## Lists

Use `1.` for every ordered-list item. This avoids accidental renumbering when items are moved or inserted.

## Diagrams

- Use a `:::mermaid` block when the diagram should render.
- Use a fenced `mermaid` block when the source should be displayed with syntax highlighting.
- Use exported SVGs from the deck's `img/` directory for richer visuals when appropriate.

## Fragments

Use `class="fragment"` to reveal content step by step. Never add `data-fragment-index`; let document order determine the sequence.

Any slide with more than a few words, a single picture, or a single table generally benefits from fragments. Apply this to every relevant block, including lists, standalone paragraphs, code blocks, and a concluding paragraph after a list.

For list items, always put the element comment on its own bullet line, with the item's text on the following indented line(s). Never put `class="fragment"` inline at the end of the item's text line, even for short, plain-text items. Inline placement breaks Markdown rendering as soon as the item contains inline markup such as `**bold**`, `` `code` ``, or `*italic*`, so always use the safe form for every list item, without exception:

```md
* <!-- .element: class="fragment" -->
  A **class** is a static blueprint, fixed at compile time.
* <!-- .element: class="fragment" -->
  An **object** is an *instance* of a class.
```

For code blocks, put the fragment comment on its own line immediately after the closing fence, with a blank line before it. Give a trailing consequence or rule-of-thumb paragraph its own fragment, after the preceding list or code block has been revealed.

## Labs
- A lab slide uses `<!-- .slide: class="is-lab" -->`, a `### Lab time!` heading, the lab's name, and the link `[↗ open lab](/labs/labNN.html)` — note the `.html` extension for what is a `.md` file. Alternatively embed as a background iframe: `<!-- .slide: data-background-iframe="/labs/labNN.html" data-background-interactive -->`.

## Quality Checks

Before finishing, verify that:

- Separators are `---` with the required blank lines; accidental `--` is absent.
- Heading levels match the presentation structure.
- Slide and element comments use documented syntax and are placed where the parser can attach them.
- HTML blocks have correct whitespace, are closed, and contain no unescaped stray angle brackets.
- Code fences have language identifiers and use four backticks when nesting fences.
- Ordered lists use `1.` consistently.
- Rendered diagrams use `:::mermaid`, while source examples use fenced `mermaid` blocks.
- Fragments use `class="fragment"` without `data-fragment-index`.
- List-item fragment comments are on separate bullet lines, and trailing paragraphs have their own fragment when needed.
- Slides fit the approximately 960 x 700 stage; long slides were shortened or split before reducing font size.
- Every chapter ends with a short recap slide, such as `98-recap.md` or `998-recap.md`.
- Lab slides use `is-lab`, a `### Lab time!` heading, the lab name, and an `/labs/labNN.html` link when applicable.
- The deck passes `npx @infosupport/kc-cli lint` when dependencies and the target deck are available.
