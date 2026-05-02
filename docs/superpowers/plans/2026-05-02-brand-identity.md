# Brand Identity Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace placeholder colors and fonts in config.yaml with the approved Deep Bloom palette and Inter/Atkinson Hyperlegible type system.

**Architecture:** The toph theme reads `params.colors` and `params.fonts` from `config.yaml`, sets them as CSS custom properties in `head.html`, and loads Google Fonts stylesheets automatically. All brand changes are config-only — no theme CSS modifications needed.

**Tech Stack:** Hugo (static site generator), toph theme (Bootstrap 5), Google Fonts

**Spec:** `docs/superpowers/specs/2026-05-02-brand-identity-design.md`

---

### Task 1: Commit design spec and gitignore update

These files were created during brainstorming and should be committed before the implementation change.

**Files:**
- Commit: `docs/superpowers/specs/2026-05-02-brand-identity-design.md` (new)
- Commit: `.gitignore` (modified — added `.superpowers/` ignore)

- [ ] **Step 1: Review staged files**

```bash
git status
git diff .gitignore
```

Verify `.gitignore` has the `.superpowers/` entry added near the top, and the spec file exists at `docs/superpowers/specs/2026-05-02-brand-identity-design.md`.

- [ ] **Step 2: Commit**

Stage and commit both files. The commit message should note that these are brainstorming artifacts for the brand identity work. Follow the project's gitmoji + component + summary format.

```bash
git add .gitignore docs/superpowers/specs/2026-05-02-brand-identity-design.md
git commit -m "📝 brand: Add Deep Bloom brand identity design spec

Document the approved color palette (pink #AD1457, cream #F8F6F0,
matcha #33691E) and typography system (Inter + Atkinson Hyperlegible
Next) based on team feedback from Jona Azizaj (color direction) and
Bee Padalkar (accessibility guidance). Also gitignore .superpowers/
brainstorming artifacts.

Assisted-by: Claude Opus 4.6 (1M context)"
```

---

### Task 2: Update colors in config.yaml

**Files:**
- Modify: `config.yaml:106-109` (the `params.colors` block)

- [ ] **Step 1: Update the three color values**

In `config.yaml`, change the `colors` block from:

```yaml
  colors:
    primary: saffron
    secondary: linen
    accent: darkslateblue
```

to:

```yaml
  colors:
    primary: "#AD1457"
    secondary: "#F8F6F0"
    accent: "#33691E"
```

The hex values must be quoted in YAML because `#` is the comment character.

- [ ] **Step 2: Verify Hugo accepts the config**

```bash
hugo config | grep -A3 'colors'
```

Expected output should show the three hex values without YAML parsing errors.

---

### Task 3: Update fonts in config.yaml

**Files:**
- Modify: `config.yaml:111-116` (the `params.fonts` block)

- [ ] **Step 1: Update font names and weights**

In `config.yaml`, change the `fonts` block from:

```yaml
  fonts:
    default: Open Sans
    default_weight: ":wght@500"
    title: Bungee Shade
    header: Roboto Slab
    header_weight: ":wght@500"
```

to:

```yaml
  fonts:
    default: Atkinson Hyperlegible Next
    default_weight: ":wght@400;700"
    title: Inter
    title_weight: ":wght@900"
    header: Inter
    header_weight: ":wght@700"
```

Note the addition of `title_weight` (not present in the original config) and the change from single weight to multi-weight for the default font.

- [ ] **Step 2: Verify Hugo accepts the config**

```bash
hugo config | grep -A7 'fonts'
```

Expected: all six font fields (default, default_weight, title, title_weight, header, header_weight) with the new values.

---

### Task 4: Visual verification with hugo server

- [ ] **Step 1: Build the site and start the dev server**

```bash
hugo server --disableFastRender
```

Open the local URL (typically `http://localhost:1313/`) in a browser.

- [ ] **Step 2: Verify each element against the spec**

Check all of these:

1. **Navbar:** Background is raspberry pink (`#AD1457`) with white text. Text is legible.
2. **Page title (h1):** Raspberry pink Inter at heavy weight (900). Should look bold and geometric.
3. **Section headers (h2–h6):** Earthy matcha green (`#33691E`) Inter at weight 700.
4. **Body text:** Atkinson Hyperlegible Next, regular weight, on the warm cream (`#F8F6F0`) background. Should feel clean and highly readable.
5. **Page background:** Warm cream (`#F8F6F0`), not pure white.
6. **Category badges:** Pink background with white text.
7. **Tag badges:** Green background with white text.
8. **Footer:** Check that it picks up the primary color appropriately.

- [ ] **Step 3: Check Google Fonts loading**

Open browser DevTools → Network tab. Confirm three Google Fonts requests load successfully:
- `family=Atkinson+Hyperlegible+Next:wght@400;700`
- `family=Inter:wght@900`
- `family=Inter:wght@700`

If any font fails to load, the page will fall back to system fonts — check the Elements panel for computed font-family values.

- [ ] **Step 4: Run a quick accessibility check**

Use the browser's Lighthouse audit (or axe DevTools extension) on the homepage.
Confirm no color contrast failures are flagged for text on the background.

---

### Task 5: Commit the config changes

**Files:**
- Commit: `config.yaml` (modified)

- [ ] **Step 1: Review the diff**

```bash
git diff config.yaml
```

Verify only the `colors` and `fonts` blocks changed — no other config values should be affected.

- [ ] **Step 2: Commit**

```bash
git add config.yaml
git commit -m "🎨 brand: Apply Deep Bloom palette and accessibility-first typography

Replace placeholder colors (saffron/linen/darkslateblue) with the
Deep Bloom palette: raspberry pink #AD1457, warm cream #F8F6F0, earthy
matcha #33691E. All combinations verified WCAG AA (5.6:1 and 6.3:1).

Replace placeholder fonts (Open Sans/Bungee Shade/Roboto Slab) with
Inter (titles 900, headers 700) for bold geometric headings and
Atkinson Hyperlegible Next (body 400/700) for maximum readability.

Design spec: docs/superpowers/specs/2026-05-02-brand-identity-design.md
Color direction: Jona Azizaj. Accessibility guidance: Bee Padalkar.

Assisted-by: Claude Opus 4.6 (1M context)"
```
