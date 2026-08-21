# Brand assets

Nothing in this directory is hand-drawn or hand-edited. Every file is the same
mark, palette and typesetting as <https://bridgingaiandsociety.org>, baked out
of the website repo so the org profile and the site can't drift apart — which
is exactly what happened once already, when the site moved off its old crimson
and these files stayed behind.

**Source of truth** is the website repo
([`bridgingaiandsociety.github.io`](https://github.com/bridgingaiandsociety/bridgingaiandsociety.github.io)):

- `_sass/_bas-brand.scss` — the accent. Lapis blue, `#416bcc` on light grounds
  and `#7a98dc` on dark; the ink is `#1c2025`. Two lightnesses because no
  single hex clears 4.5:1 on both grounds. That partial's header comment
  carries the full rationale, including the colour-vision numbers.
- `tools/build-brand-assets.py` — the geometry, and the outlining of the text.
  Baked text is drawn from vendored Roboto outlines rather than set in a
  `font-family`, so a file is the same drawing wherever it is opened.

If the accent ever moves again: change it on the site, rerun
`tools/build-brand-assets.py` there, then redo the copies and the two commands
below. Don't recolour these files in place.

## Where each file comes from

Straight copies of a website file:

| File here | Website file |
| --- | --- |
| `banner.jpg` | `assets/img/banner.jpg` |
| `og-card.png` | `assets/img/og-card.png` |
| `logo.png` | `assets/favicon/web-app-icon-512x512.png` |
| `logo-lockup.svg` | `assets/img/bas-logo.svg` |
| `logo-lockup-on-dark.svg` | `assets/img/bas-logo-on-dark.svg` |
| `headshot-christoph.jpg`, `headshot-knut.jpg` | `assets/img/…` (same files) |

Baked from the website's script, for sizes the site itself has no use for —
run from a checkout of the website repo, with
`python3 -m pip install -r tools/requirements.txt` and `brew install librsvg`:

```python
import importlib.util
spec = importlib.util.spec_from_file_location("bas", "tools/build-brand-assets.py")
bas = importlib.util.module_from_spec(spec); spec.loader.exec_module(bas)

# avatar.png — the org's GitHub avatar. Full-bleed tile, no corner radius:
# GitHub applies its own circular mask. The mark sits at the touch-icon scale
# so that mask can't clip it.
bas.render(bas.mark_svg(1024, tile="square", scale=0.66), "avatar.png")

# logo.svg — the mark on its rounded tile, vector. The favicon drawing at a
# size that wants the display cut rather than the small one.
open("logo.svg", "w").write(bas.mark_svg(512) + "\n")

# rule.svg / rule-on-dark.svg — the accent rule from under the wordmark on the
# social card (og_svg()); same geometry, one cut per ground.
for name, accent in (("rule.svg", bas.ACCENT), ("rule-on-dark.svg", bas.DARK_ACCENT)):
    open(name, "w").write(
        '<svg xmlns="http://www.w3.org/2000/svg" width="72" height="7" '
        f'viewBox="0 0 72 7"><rect width="72" height="7" rx="3.5" fill="{accent}"/></svg>\n')
```

## Two cuts, and why the README uses `<picture>`

A baked file can't follow a palette, so anything whose ink or accent has to
change with the ground ships twice — `logo-lockup.svg` /
`logo-lockup-on-dark.svg`, `rule.svg` / `rule-on-dark.svg`. `profile/README.md`
picks between them with

```html
<picture>
  <source media="(prefers-color-scheme: dark)" srcset="…-on-dark.svg">
  <img src="….svg" alt="…">
</picture>
```

so the profile reads correctly in both GitHub themes, the way the site does in
both skins.

`avatar.png`, `logo.png`, `logo.svg` and `og-card.png` need no dark cut: they
carry their own opaque light ground, and so read as framed pictures on either
theme. `banner.jpg` likewise. That ground is deliberate rather than
transparent — dark ink on a transparent tile vanishes into dark chrome.
