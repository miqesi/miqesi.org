[miqesi.org](https://miqesi.org)
================================

[![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC_BY--NC--SA_4.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/)

Homepage of Project Miqësi


## What

This repository is the homepage for Project Miqësi.
It is a [Hugo](https://gohugo.io) site with a custom theme called Toph.


## Development

### Requirements

- Hugo `0.169.0` or later.
  The theme itself requires `0.166.0` as a minimum.
- [Asciidoctor](https://asciidoctor.org) for `.adoc` content.
- Network access during the build.
  The theme fetches Google Fonts at build time and republishes them locally.
  If the fetch fails, the theme warns and falls back to the Google Fonts CDN link.

### First-time setup

The theme is a git submodule.
Clone with submodules, or initialize them after cloning:

```bash
git submodule update --init --recursive
```

`.gitmodules` points at the HTTPS URL so fresh clones and CI need no SSH key.
This local clone keeps an SSH override in `.git/config`.
Do not run `git submodule sync` without re-applying it:

```bash
git config --local submodule.themes/toph.url git@github.com:justwheel/toph-hugo-theme.git
```

### Local development

```bash
hugo server
```

### Production build

```bash
hugo --gc --minify
```

### Updating the theme

```bash
git submodule update --remote --rebase
git add themes/toph
```

Review the staged pointer and commit it with the rest of the change.

### Assets

Page assets live in `assets/pages/` and are served at `/pages/` through `module.mounts`.
That mount is what lets Hugo process images into responsive WebP derivatives.
The site logo is `assets/pages/logo.png`; the hero generates 280w and 500w WebP versions from it.
The original PNG is still the `og:image` and JSON-LD fallback.


## Contributing

See `CONTRIBUTING.md` for how to add a blog post.


## Legal

All content in this repository is licensed under the [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-nc-sa/4.0/) license (CC BY-NC-SA 4.0).
The full text is in `LICENSE.txt`.

The Toph theme is licensed separately under the [Mozilla Public License 2.0](https://www.mozilla.org/en-US/MPL/2.0/), and `themes/toph/LICENSE.txt` holds that text.
