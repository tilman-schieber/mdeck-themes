# mdeck themes

Themes and palettes for [mdeck](https://github.com/tilman-schieber/mdeck). Try
them on a sample deck, light and dark, at <https://gh.tschieber.de/mdeck-themes/>,
or from a terminal:

```sh
mdeck themes search
mdeck themes install duet                 # for every deck; brings its palette, cobalt
mdeck themes install duet my-talk.md      # beside one deck
mdeck palettes search
mdeck palettes install solarized
```

The design page (`mdeck design my-talk.md`) lists them too, with an Install
button. See [Install or share a theme](https://gh.tschieber.de/mdeck/theme-repository.html).

## Add a theme or palette

Each one is a folder of its own:

```text
themes/harbour/          extension.toml and styles.css
palettes/harbour-night/  extension.toml
```

They are ordinary mdeck extensions (see
[Create a theme or palette](https://gh.tschieber.de/mdeck/theme-authoring.html)),
with a few more settings at the top of `extension.toml`:

```toml
version = "1.0.0"          # raise it for every change
author = "Your Name"
license = "MIT"
mdeck = ">=3.1.0"          # the oldest mdeck it works with
homepage = "https://…"     # optional
```

A theme names its default palette (`palette = "harbour-night"`). That palette
must be here or come with mdeck; installing the theme installs it too. A
palette meant for one theme only says so (`theme = "harbour"`), and installing
it installs that theme. While you work on one, keep it in the `extensions/`
folder beside a deck and look at it with `mdeck design`.

**Rules**, checked for every pull request and again by mdeck when it installs:

- only `extension.toml`, and `styles.css` for a theme: no layouts, components
  or other code;
- fonts only from `https://fonts.googleapis.com/` or `https://fonts.bunny.net/`;
- no `@import` and no `url()` other than `data:` URLs in `styles.css`;
- ids unique among themes and among palettes, and different from mdeck's
  built-in ones;
- every theme and palette passes the theme check: on every kind of slide,
  light and dark, text fits and is readable.

Check yours the way the pull request will:

```sh
mdeck themes build . -o site --check
```

Then open a pull request. Pictures for the gallery are made when it is merged.
