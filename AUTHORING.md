# Writing a theme

What a theme in this repository is made of, which parts of a slide it styles,
and what it needs before it is merged. The general guide is mdeck's
[Create a theme or palette](https://gh.tschieber.de/mdeck/theme-authoring.html);
the rules for this repository are in the [README](README.md#add-a-theme-or-palette).
This page fills the gap between them: the classes a stylesheet has to cover.

## Start from a copy

Copy a theme close to what you want, rename the folder, and change `id`,
`title`, `description`, `version = "0.1.0"` and `author`:

- mdeck's `minimal` (`assets/extensions/themes/minimal/` in the mdeck
  repository) is the shortest complete theme: every class below, no
  decoration, one comment per section. Start here when in doubt.
- In this repository, pick by what the theme does: `lightbox` for pictures,
  `terminal` for code, `editorial` for text.

Work on it beside a deck, in `extensions/<id>/`, and look at it with
`mdeck design my-talk.md`: every kind of slide, light and dark.

## The manifest

`extension.toml` holds identity, fonts, default palette, tokens, parameters
and the `[guide]`. A theme sets these tokens; every stylesheet in this
repository uses them, and mdeck's own base styles use some of them too:

| Token | Used for |
|---|---|
| `--fs-display` | Title slide heading |
| `--fs-title` | Chapter slide heading |
| `--fs-focus` | Focus slide statement |
| `--fs-h` | Heading of an ordinary slide |
| `--fs-sub` | Subtitles, second-level headings, display maths |
| `--fs-body` | Body text and lists |
| `--fs-small` | Footer, captions, attributions |
| `--fs-eyebrow` | Small labels above headings |
| `--pad-x`, `--pad-y` | Slide margins |
| `--gap-title` | Space below a slide heading |
| `--gap-item` | Space between paragraphs and list items |
| `--font-display`, `--font-body` | The two typefaces |

Sizes are in pixels on a 1920 × 1080 slide. A theme may add tokens of its
own (lightbox has `--photo-share`); make one a `[params.name]` when authors
should be able to change it. Offer `fontDisplay` and `fontBody` as
parameters, as most themes here do.

## The slide

Every slide is a `<section>` with the same frame; layouts fill `.slide-body`.

```text
section.slide.slide--<layout>  [.has-image] [.has-overlay]
  .slide-header       .brand, .slide-logo (title slide), the section name
  .slide-body         the layout's content
  .slide-footnotes    when the slide has footnotes
  .slide-citations    when it cites works
  .slide-footer       author and date, slide number (.slide-footer--no-meta without the first)
```

`.has-image` is set on title and chapter slides with an `image:`;
`.has-overlay` on full-bleed slides with `overlay:`.

### Layouts and their classes

| Layout | Classes inside `.slide-body` |
|---|---|
| `title` | `.title-text` › `h1.display`, `p.subtitle`; `.title-image` |
| `chapter` | `.chapter-content` › `.chapter-meta`, `.chapter-num`, `.chapter-title`, `.chapter-desc`; `.chapter-image` |
| `focus` | `.focus-eyebrow`, `.focus-content` (holds `h1` or `p`, or `.focus-statement`), `.focus-attribution` |
| `image-text` | `.image-pane` (or `.image-placeholder`), `.text-pane` › `.text-content`, `.eyebrow`, `.title`, `.body-text` |
| `split` | `.split-heading` (when named regions are used), `.split-columns` › `.split-left`, `.split-right` |
| `full-bleed-image` | `.slide-bg` › `.slide-bg-picture`; `.overlay-title` |
| content (no layout) | plain Markdown: `h1`, `h2`, `p`, `ul`, `ol`, `blockquote`, `table`, pictures |

Inside any slide body, also style:

- **callouts**: `.callout`, `.callout-title`, and the kinds `.callout-note`,
  `-tip`, `-important`, `-warning`, `-caution`, and for lectures `-definition`,
  `-theorem`, `-lemma`, `-corollary`, `-proposition`, `-example`, `-remark`,
  `-proof`;
- **code**: `.code-block` (set `--code-bg`, `--code-radius`, `--code-size`);
- **maths**: `.katex-display`;
- **audience questions**: `.poll-track`, `.poll-fill`, `.scale-track`,
  `.scale-fill`, `.question-cards`, `.poll-join`;
- **lists**: `ul > li::before` and `ol > li::before` (the counter is
  `slide-ol`).

mdeck's base styles only arrange the slide (the frame, panes, columns, list
spacing, footnotes); type, colour and decoration are all the theme's. A class
the theme leaves alone is unstyled text, so cover every row above.

## Colours

The stylesheet has no colour values of its own: it uses the palette's roles
(`--bg`, `--surface`, `--ink`, `--ink-soft`, `--muted`, `--rule`, `--accent`,
`--accent-2`, `--on-accent`), mixes of them (`color-mix(in oklab, …)`), and
`--inverse-*` for an inverted slide. This is what lets every palette repaint
the theme.

Two exceptions are fine: white text on a photograph with a dark shadow behind
it (full-bleed and image slides, both appearances), and plain black or white
shadows. Palettes may also set `--token-*` (code), `--callout-*` and
`--logo-filter`; use those variables rather than picking colours for code and
callouts yourself.

## Before the pull request

- Look at every layout in `mdeck design`, light and dark, with a long title,
  a long list, a picture, code, a table and a footnote.
- Write the `[guide]`: what the theme suits, what it does not, and how to
  write slides for it (see the README).
- Give `styles.css` a header comment saying what the theme is and what it
  does differently; the existing themes show the tone.
- Raise `version` for every change after the first merge.
- Run the check the pull request runs:

  ```sh
  mdeck themes build . -o site --check
  ```
