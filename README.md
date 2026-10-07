# mdeck themes

Themes and palettes for [mdeck](https://github.com/tilman-schieber/mdeck), shared
as **packs**. Browse them at <https://gh.tschieber.de/mdeck-themes/>, or from a
terminal:

```sh
mdeck themes search
mdeck themes install solarized my-talk.md      # beside the deck
mdeck themes install solarized --global        # for every deck
```

The design page (`mdeck design my-talk.md`) lists them too, with an Install
button. See [Install or share a theme](https://gh.tschieber.de/mdeck/theme-repository.html).

## Add a pack

A pack is a folder in `packs/` with a `pack.toml` and one folder per theme or
palette:

```text
packs/harbour/
  pack.toml
  harbour/            a theme: extension.toml and styles.css
  harbour-night/      a palette: extension.toml
```

```toml
schema = 1
id = "harbour"                # the folder name
title = "Harbour"
description = "Navy and brass, with a serif for headings."
version = "1.0.0"             # raise it for every change
author = "Your Name"
license = "MIT"
mdeck = ">=2.3.0"             # the oldest mdeck it works with
```

The theme and palette folders are ordinary mdeck extensions; see
[Create a theme or palette](https://gh.tschieber.de/mdeck/theme-authoring.html).
While you work on one, keep it in the `extensions/` folder beside a deck and
look at it with `mdeck design`.

**Rules**, checked for every pull request and again by mdeck when it installs a pack:

- only `extension.toml`, and `styles.css` for a theme: no layouts, components
  or other code;
- fonts only from `https://fonts.googleapis.com/` or `https://fonts.bunny.net/`;
- no `@import` and no `url()` other than `data:` URLs in `styles.css`;
- ids unique across the repository and different from mdeck's built-in ones;
- every theme and palette passes the theme check: on every kind of slide,
  light and dark, text fits and is readable.

Check a pack the way the pull request will:

```sh
mdeck themes build packs -o site --check
```

Then open a pull request. Pictures for the gallery are made when it is merged.
