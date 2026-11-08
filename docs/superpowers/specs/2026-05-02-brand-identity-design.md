# Miqesi Brand Identity — Color Scheme and Typography

## Overview

This spec defines the color palette and typography for miqesi.org, replacing the placeholder values (saffron/linen/darkslateblue, Open Sans/Bungee Shade/Roboto Slab) established during the initial site setup.

**Design goals:**

- Bold and energetic brand personality
- Pink and matcha green color pairing (per team feedback from Jona Azizaj)
- WCAG AA accessibility compliance with minimum 4.5:1 contrast ratios (per accessibility guidance from Bee Padalkar)
- Accessible, legibility-first font choices (per Bee Padalkar's recommendations)

## Color Palette — "Deep Bloom"

| Role | Hex | Description | Contrast on secondary |
|------|-----|-------------|----------------------|
| Primary | `#AD1457` | Deep raspberry pink | 5.6:1 (WCAG AA) |
| Secondary | `#F8F6F0` | Warm cream background | — |
| Accent | `#33691E` | Deep earthy matcha green | 6.3:1 (WCAG AA) |

**How the theme uses each color:**

- **Primary (`#AD1457`):** Navbar background (with white text), page titles (h1), category badges.
White text on this background achieves 5.6:1 contrast (WCAG AA).
- **Secondary (`#F8F6F0`):** Page background color.
Provides a warm, non-sterile canvas that pairs well with both brand colors.
- **Accent (`#33691E`):** Section headers (h2–h6), tag badges, secondary emphasis elements.
At 6.3:1 contrast on the background, this has the strongest accessibility margin.

**Why "Deep Bloom" over brighter alternatives:**

Brighter pinks (e.g., `#E91E63`) fail WCAG AA for white text on colored backgrounds.
A lighter matcha green (e.g., `#558B2F`) fails WCAG AA for text on light backgrounds at 4.0:1.
Deep Bloom maximizes vibrancy while maintaining comfortable contrast margins above the 4.5:1 threshold.

## Typography

| Slot | Font | Weight | `params.fonts.*_weight` |
|------|------|--------|-------------------------|
| Title (h1) | Inter | 900 (Black) | `"900"` |
| Header (h2–h6) | Inter | 700 (Bold) | `"700"` |
| Body (default) | Atkinson Hyperlegible Next | 400 (Regular) | `"400"` |

**Font-weight binding:** Toph's `partials/font-weight.html` treats a value beginning with `:` as a legacy Google Fonts query and uses the **last** weight in the tuple for CSS.
Configuring `:wght@400;700` therefore rendered body text at 700 instead of 400.
Use numeric strings instead; the theme builds the `:wght@` request from them, and browsers synthesize bold body text.

**Font rationale:**

- **Inter** (titles and headers): Designed specifically for screens.
At heavy weights (900, 700), it is sharp, geometric, and confident — fitting the bold and energetic brand personality.
Using one font family across all heading levels creates visual cohesion.
- **Atkinson Hyperlegible Next** (body text): Developed by the Braille Institute for maximum legibility.
Distinctive letterforms (e.g., differentiated `I`, `l`, `1`) reduce reading errors.
The "Next" revision (2024) improves on the original with better weight range and refinement.
This directly follows Bee Padalkar's accessibility recommendation.

**Accessibility formatting guidelines (from Bee Padalkar's feedback):**

- Minimum 16px body text (the theme already uses this as the base)
- Left-aligned text (avoid justified)
- Bold for emphasis instead of italics
- Minimum 1.15 line spacing (the theme uses 1.6+ for body text)
- Proper heading hierarchy (h1–h6) for screen reader navigation

## Implementation

All changes are confined to `config.yaml` under the `params` section.
No theme CSS files need modification.

The theme's `head.html` partial reads these values and compiles them into the stylesheet.
Light-mode brand and neutral values are baked in at build time through `css.Build` vars (`@import 'hugo:vars'`), and dark-mode values are emitted as an inline `<style>` block.
Fonts are fetched at build time and republished from the site's own origin.

**Config changes:**

```yaml
params:
  color_mode: auto
  colors:
    light:
      primary: "#AD1457"
      secondary: "#F8F6F0"
      accent: "#33691E"
      background: "#F8F6F0"
    dark:
      primary: "#AD1457"
      secondary: "#F8F6F0"
      accent: "#33691E"
      background: "#121212"
  fonts:
    default: Atkinson Hyperlegible Next
    default_weight: "400"
    title: Inter
    title_weight: "900"
    header: Inter
    header_weight: "700"
  hero:
    shape: square
```

`colors.background` is a required role in the nested schema.
`colors.dark.*` may fall back to its light counterpart, but both trees are declared here so dark mode is explicit.

**What changes from the original implementation:**

| Field | Before | After |
|-------|--------|-------|
| `colors.primary` | `saffron` | `colors.light.primary: "#AD1457"` |
| `colors.secondary` | `linen` | `colors.light.secondary: "#F8F6F0"` |
| `colors.accent` | `darkslateblue` | `colors.light.accent: "#33691E"` |
| `colors.background` | *(not a role)* | `colors.light.background: "#F8F6F0"`, `colors.dark.background: "#121212"` |
| `fonts.default` | `Open Sans` | `Atkinson Hyperlegible Next` |
| `fonts.default_weight` | `:wght@500` | `"400"` |
| `fonts.title` | `Bungee Shade` | `Inter` |
| `fonts.title_weight` | *(not set)* | `"900"` |
| `fonts.header` | `Roboto Slab` | `Inter` |
| `fonts.header_weight` | `:wght@500` | `"700"` |
| `hero.shape` | *(not set — circular crop)* | `square` |

## Assets

The brand mark lives at `assets/pages/logo.png` and is served at `/pages/logo.png`.
`module.mounts` maps `assets/pages` to `static/pages`, which keeps the file available to Hugo's image pipeline while giving it a stable public URL.

The hero resizes the source into 280w and 500w WebP derivatives at build time.
The source PNG is not re-encoded and remains the `og:image` and JSON-LD fallback.
`params.hero.shape: square` keeps the circular CSS mask from clipping the square mark.

## Verification

After applying the config change:

1. Run `hugo server` locally and verify the site renders with the new colors and fonts
2. Check navbar: raspberry pink background with legible white text
3. Check h1: raspberry pink Inter 900 on cream background
4. Check h2–h6: matcha green Inter 700 on cream background
5. Check body text: Atkinson Hyperlegible Next, readable at default size
6. Check category badges (pink background) and tag badges (green background) for white text legibility
7. Run a browser accessibility audit (Lighthouse or axe) to confirm WCAG AA compliance

## Design Credits

- Color direction (pink + matcha green): Jona Azizaj
- Accessibility guidance (fonts, contrast, formatting): Bee Padalkar
