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
parameters, as every theme here does.

### Parameters

Every parameter says what kind of value it takes, with `type` (mdeck 3.9.0
and newer). mdeck then checks what authors write, the editor and the gallery
show a slider, a box to tick or a list, and `mdeck list themes` shows it.
Every theme here takes the same kinds of value:

| Kind | `type` | Value | Off | Example |
|---|---|---|---|---|
| A size | `size`, with `min` and `max` | pixels | `0px` | pop's `edge` |
| A strength | `number`, with `min = 0` and `max = 1` | a number from 0 to 1 | `0` | riso's `grain` |
| A switch | `switch` | `1` or `0` | `0` | bauhaus's `shapes` |
| A font | `font` | a CSS font list | | every theme's `fontBody` |

```toml
[params.bar]
token = "--bar"
title = "Thickness of the bar under headings"
type = "size"
min = 0
max = 20
description = "12px by default, from about 6px to 20px; 0px leaves headings without bars."
```

- The token's value must be of that kind (`"12px"`, `"1"`); the theme check
  fails otherwise.
- `min` and `max` are the range the theme was made for: look at both ends
  in `mdeck design` before you write them down.
- No CSS keywords (`none`, `block`) and no `em`: an author should not need
  to know CSS to change a value. (`choice`, with `choices = [...]`, exists
  for a real choice between named options, not for on and off.)
- Write `0px` for a size, not `0`: a bare `0` breaks a `calc()` that adds
  pixels to it.
- A feature that can be left out is turned off with its parameter at zero;
  do not add a second parameter for that.
- A switch or strength is used by multiplying with it, as in
  `calc(330px * var(--shapes))`.
- The `description` gives the default and a sensible range: the editor and
  the gallery show it beside the control, and assistants read it.
- A theme with typed parameters sets `mdeck = ">=3.9.0"`.

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
`--accent-2`, `--accent-3`, `--on-accent`), mixes of them
(`color-mix(in oklab, …)`), and `--inverse-*` for an inverted slide. This is
what lets every palette repaint the theme.

The three accents mean something, and a theme uses them for that:

| Accent | Meaning | Callouts |
|---|---|---|
| `--accent` | Emphasis | note, tip, important |
| `--accent-2` | The accent's companion: gradients, a contrast to the accent | definitions |
| `--accent-3` | Attention; pops, so use it sparingly | warning, caution |

`--accent-3` marks things (bars, shapes, tinted backgrounds) and is never set
as text: a palette may make it a yellow that cannot be read on the
background. For a callout title in it, mix it with the ink, as mdeck does
(`color-mix(in oklab, var(--accent-3) 45%, var(--ink))`).

Every palette sets all three; the one-colour palettes (`phosphor` here,
`greyscale` in mdeck) use shades of that colour.

Two exceptions are fine: white text on a photograph with a dark shadow behind
it (full-bleed and image slides, both appearances), and plain black or white
shadows. Palettes may also set `--token-*` (code) and `--logo-filter`; use
those variables rather than picking colours for code yourself.

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
