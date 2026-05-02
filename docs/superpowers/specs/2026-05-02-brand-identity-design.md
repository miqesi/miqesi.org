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

| Slot | Font | Weight | Google Fonts weight param |
|------|------|--------|--------------------------|
| Title (h1) | Inter | 900 (Black) | `:wght@900` |
| Header (h2–h6) | Inter | 700 (Bold) | `:wght@700` |
| Body (default) | Atkinson Hyperlegible Next | 400 (Regular), 700 (Bold) | `:wght@400;700` |

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

The theme's `head.html` partial reads these values, sets them as CSS custom properties (`--primary`, `--secondary`, `--accent-color`, `--default-font`, `--title-font`, `--header-font`), and loads the corresponding Google Fonts stylesheets.

**Config changes:**

```yaml
params:
  colors:
    primary: "#AD1457"
    secondary: "#F8F6F0"
    accent: "#33691E"
  fonts:
    default: Atkinson Hyperlegible Next
    default_weight: ":wght@400;700"
    title: Inter
    title_weight: ":wght@900"
    header: Inter
    header_weight: ":wght@700"
```

**What changes from the current config:**

| Field | Before (placeholder) | After |
|-------|---------------------|-------|
| `colors.primary` | `saffron` | `"#AD1457"` |
| `colors.secondary` | `linen` | `"#F8F6F0"` |
| `colors.accent` | `darkslateblue` | `"#33691E"` |
| `fonts.default` | `Open Sans` | `Atkinson Hyperlegible Next` |
| `fonts.default_weight` | `:wght@500` | `:wght@400;700` |
| `fonts.title` | `Bungee Shade` | `Inter` |
| `fonts.title_weight` | *(not set)* | `:wght@900` |
| `fonts.header` | `Roboto Slab` | `Inter` |
| `fonts.header_weight` | `:wght@500` | `:wght@700` |

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
