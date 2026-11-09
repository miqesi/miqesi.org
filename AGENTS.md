# AGENTS.md

Guidance for AI coding agents working in the `miqesi.org` repository.
This file covers the site, including its editorial policy, taxonomy decisions, and deployment; `themes/toph/AGENTS.md` covers what holds for any downstream site.

## Project Overview

- This repository is the homepage of Project Miqësi, built with Hugo and the Toph theme.
- The site deploys to GitHub Pages at `https://miqesi.org/`.
- All content is licensed [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/), and `LICENSE.txt` holds that license text.
- The Toph theme is licensed MPL-2.0, and the submodule carries its own `LICENSE.txt`.
- Hugo `0.169.0` extended is pinned locally and in CI; the theme requires at least `0.166.0`.
- The site is early-stage: the blog has no posts, and the homepage still reads "Coming soon…".

## Two-Repository Architecture

- The site repository owns content, configuration, static and page assets, CI, and documentation.
- `themes/toph/` is a submodule of [`justwheel/toph-hugo-theme`](https://github.com/justwheel/toph-hugo-theme); templates and styles belong there.
- `themes/toph/AGENTS.md` is authoritative for everything inside the theme.
- Run commands from the repository root.
- Use `git -C themes/toph ...` for theme operations rather than changing directories.
- When presenting commands to the maintainer, do not include `cd`; name the directory once and show bare commands.

### Deliberate duplication

Two sections are duplicated here and in `themes/toph/AGENTS.md`: the git workflow and the GitHub write-consent protocol.
An agent may read only one of the two files, and both rules are safety-critical.
Keep them synchronized when either file changes.
Any other disagreement between the two files is a bug to reconcile, not a precedence question.

## Common Developer Commands

```bash
hugo server                 # drafts excluded
hugo server -D              # include drafts
hugo --gc --minify          # production-shaped build
hugo --gc --minify --destination /tmp/agents/<harness>/site-check
hugo --logLevel info --destination /tmp/agents/<harness>/site-check
git submodule update --init --recursive
git submodule update --remote --rebase
```

- Asciidoctor must be installed for any build that includes `.adoc`; a missing binary fails the build outright.
- The build needs network access because the theme fetches Google Fonts and republishes them locally.
- If the font fetch fails, the theme warns and falls back to the Google Fonts CDN link.
- Build to a throwaway destination when verifying a change; `public/` is gitignored and can hold stale output.
- `--logLevel info` prints one upstream deprecation notice; see "Known Defects and Follow-ups".

### Temporary files

Write scratch files, payloads, and commit messages under `/tmp/agents/<harness>/`; this checkout is OpenCode, so that is `/tmp/agents/opencode/`.
Remove large build destinations when they are no longer needed.
`/tmp` is periodically cleaned, so rewrite a commit message file if it disappears before it is used.
Write each artifact once; `/tmp` is RAM-backed, so do not mirror the same file to a second path.

## Content Architecture

```text
content/
├── _index.en.adoc                     layout: biography; "Coming soon…" body
├── blog/_index.en.md                  introduction and meta description; no posts yet
├── categories/{footer,projects,team}/_index.md
├── footer/_index.md                   hide_sitemap: true
├── footer/1-conservancy-sustainer.adoc
├── projects/_index.md                 hide_sitemap: true
├── projects/01-example.en.md
└── team/                              seven member profiles plus _index.en.md
```

- Blog posts, when they exist, live at `content/blog/YYYY/MM/slug.en.md` or `.en.adoc`, as documented in `CONTRIBUTING.md`.
- The URL comes from the path and the optional `slug`; the archive groups posts by `date`, not by folder.
- Team pages are ordered by `weight` (`10`–`70`), carry `categories: ["team"]`, and set `hide_sitemap: true`.
- Only `content/team/justin.en.md` has a `photo`, and it is a remote URL under `jwheel.org`; the other six have no photo.
- `projects/` and `footer/` are structural sections: their section pages and taxonomy-term pages each need `hide_sitemap: true` because the theme's sitemap template reads the key per page.
- The homepage `content/_index.en.adoc` must keep `layout: biography`; without it Hugo silently renders a plain list of regular pages.
- Do not set `weight` on a blog post; posts sort by date.
- Do not set `hide_sitemap: true` on a blog post; the archive filters those pages out.

### Archetypes belong to the theme

- `themes/toph/archetypes/blog.md` and `themes/toph/archetypes/blog.adoc` scaffold the full front matter.
- This repository has no `archetypes/` directory, and none should be added.
- `hugo new` resolves the theme's archetypes automatically for both formats.
- The AsciiDoc archetype omits `toc` because `:toc:` is the native control, and it carries a `// Add :toc:` hint.
- Hugo selects an archetype by file extension, so a change to one blog archetype usually belongs in both.
- Creating `foo.md` and `foo.adoc` with the same basename makes Hugo treat them as conflicting pages and write its "Content Placeholder" stub for the second file.
- Use distinct basenames, or the `.en.` suffix, when probing archetypes.

### Asset pipeline

- `config.yaml` mounts `static` to `static` and `assets/pages` to `static/pages`.
- Page images live under `assets/pages/` and are referenced as `/pages/<name>`; the mount is what lets Hugo process them while still serving a stable URL.
- Do not move a file from `assets/pages/` into `static/` to "fix" a reference; that bypasses image processing.
- `assets/pages/logo.png` is the site logo, the hero image, and the `og:image` fallback.
- Hugo generates 280w and 500w WebP derivatives from it.
- The original PNG is 6.0 MiB and is served as-is for social metadata; that trade-off is accepted.

### Build artifacts

- `public/` holds the rendered site and `resources/_gen/` holds the Hugo resource cache; both are gitignored.
- `.hugo_build.lock` is generated and ignored.
- Never edit generated files by hand.

## AsciiDoc Parity

- AsciiDoc is a first-class content format; `CONTRIBUTING.md` presents it as the advanced alternative to Markdown.
- Detect the format with `.File.Ext == "adoc"`, never with `.Markup`.
- Asciidoctor bypasses Hugo render hooks, so the two formats reach the same HTML by different routes and both must be verified.
- `markup.asciidocExt.preserveTOC: true` is what makes `:toc:` render a table of contents.
- The theme centralizes page-body rendering so AsciiDoc receives compatibility processing outside the single-post layout.
- Do not add a site-level layout override that bypasses that contract.

## Hugo Template Conventions

- This repository has no `layouts/` directory; template work should be contributed back upstream in the theme whenever possible.
- If a site-level override ever becomes necessary, use `site.Params`, `site.Title`, and `site.BaseURL`, never `$.Site` or `.Site`.
- Hugo 0.169.0 deprecates reading reserved front-matter fields through `.Params` and `.Param`.
- The theme still does this in a few templates, which is why `--logLevel info` reports one notice; do not fix it from this repository.

## Content Language

- The site publishes one language: `en`, with `defaultContentLang: en` and `languages.en.weight: 1`.
- Content filenames keep the `.en.` infix even with a single language, so translations can be added without renaming files.
- Adding a language means giving it weight `2` or higher and leaving `defaultContentLang` alone.
- Arabic, if added later, is right-to-left and must be checked visually before shipping.
- UI strings live in the theme's `i18n/` files, not in this repository.

## Site Configuration

A few keys in `config.yaml` change behavior in ways an agent will otherwise trip over.

| Key | Value | Consequence |
|---|---|---|
| `enableGitInfo` | `true` | `.Lastmod` and sitemap metadata come from git history |
| `params.colors` | nested `light:` / `dark:` | Both palettes need a `background` token |
| `params.fonts` | numeric weights | Use `"400"`, `"700"`, `"900"`, not `:wght@...` strings |
| `params.hero.shape` | `square` | Keeps the square mark uncropped |
| `params.social.github` | `miqesi` | A username, not a URL; the registry builds the link |
| `params.taxonomy_exclude` | `footer`, `projects`, `team` | Keeps those sections out of taxonomy listings |
| `params.biography.email` | `example@example.com` | Deliberate RFC 2606 placeholder; see known defects |
| `params.images` | `/pages/logo.png` | OpenGraph and JSON-LD image |
| `params.legal.license` | CC BY-NC-SA 4.0 | Footer content-license link |
| `params.team.page_title` | `Team` | Team section heading |
| `menu.nav` | five `pageRef` entries | Subpath-aware menu; no hardcoded URLs |
| `markup.asciidocExt.preserveTOC` | `true` | `:toc:` renders |
| `security.exec.allow` | includes `^asciidoctor$` | Required for `.adoc` builds |

- `params.biography.name` and `params.biography.knows_about` feed the JSON-LD `Person` block.
- `params.git_repo` drives the footer's "source code" link.

## Deployment

- `.github/workflows/hugo.yaml` runs on every push, every pull request, and `workflow_dispatch`.
- There is no `schedule` trigger, so a future-dated post will not appear until a push or a manual run.
- The `build` job installs Hugo `0.169.0`, Dart Sass `1.108.1`, and Asciidoctor, then runs `hugo --gc --minify` with `HUGO_ENVIRONMENT=production` and `TZ=America/New_York`.
- The `deploy` job is gated to `refs/heads/main`; it shows as skipped on pull requests, which is expected noise.
- `--baseURL` is injected from the Pages configuration; never hardcode a deployment URL in `config.yaml`.
- GitHub Pages serves the site at the custom domain `miqesi.org`, with HTTPS enforced.
- Pages sets `cache-control: max-age=600` on assets; a caching finding in a performance audit is not actionable without a CDN.
- Direct pushes to `main` are the established workflow, and the branch is not protected.
- Deployment is not instant: confirm the Pages run and the live `last-modified` before comparing production output to a local build.

## Theme Submodule

- `themes/toph/` is pinned to a specific commit, and that pointer is part of this repository's history.
- The current pin is `1334041` (`v1.2.1-10-g1334041`), ten commits past the latest release, `v1.2.1`.
- Pinning a commit between releases is normal here; the bump commit should say why.
- `.gitmodules` uses the HTTPS URL so fresh clones and CI need no SSH key.
- A theme-only bump is correct when the theme commit is self-contained, as with the blog archetypes in `1ab9ae1`.
- A bump that a site change depends on belongs in the same commit as that site change.
- Uncommitted edits inside `themes/toph/` never ride along in a site commit; they must land upstream first.
- The theme's `theme.toml` declares `min_version = "0.166.0"`; that is a support floor for downstream users, so do not raise it to match the local build.

## Verification and State Checking

- Prove state before asserting: run the proving command first.
- A negative grep proves absence, not correctness.
- Enumerate every surface before claiming exhaustiveness.
- Preferred build check:

```bash
rm -rf /tmp/agents/opencode/site-check
hugo --gc --minify --destination /tmp/agents/opencode/site-check
echo $?
```

- The build must exit `0` with no `WARN` or `ERROR` lines at default log level.
- A clean build produces exactly five sitemap URLs: `/`, `/blog/`, `/categories/`, `/tags/`, and `/team/`.
- Verify metadata as well as the visible page:

```bash
grep -o '<loc>[^<]*' /tmp/agents/opencode/site-check/sitemap.xml
grep -o 'application/ld+json' /tmp/agents/opencode/site-check/index.html
```

- Parse the JSON-LD blocks with a JSON parser before trusting them.
- When touching archetypes, probe both formats and delete the probe files afterward.
- Confirm the live site and the Pages run after a push; every push to `main` deploys.

## Git Workflow and Conventions (CRITICAL)

- Never run `git commit`, `git push`, `git merge`, or `git tag`; the maintainer runs them.
- Never create a pull request.
- Stage with a path or glob, and check `git status --porcelain` first so an in-progress file is not captured.
- Do not run `git reset` without permission.
- Commit format is `<gitmoji> <component>: <summary>`.
- Components in use include `blog`, `team`, `home`, `config`, `ci`, `assets`, `seo`, `docs`, `theme`, and `theme(toph)`.
- Keep the subject at or under 50 characters and wrap body lines at 72.
- Wrap body lines greedily: pack as many words as fit without reaching 73 characters.
- Explain why the change exists in the body, not what the diff does.
- Wrap paths, identifiers, and code entities in backticks, and never split backticks across lines.
- Leave commit hashes bare so GitHub autolinks them.
- Add the trailer `Assisted-by: <model> (<context window>)`.
- Read the model and context window from the live session; never copy them from this file or a previous message.
- Ask for `--signoff` rather than writing `Signed-off-by` by hand.
- GPG-sign every commit with `--gpg-sign`, and never use `--no-gpg-sign` or skip hooks.
- Draft commit messages to `/tmp/agents/opencode/commit-<name>.txt`.
- Confirm `grep -c '^#'` returns `0` for the message file, because Git strips lines beginning with `#`.
- Present the command as one unbroken line:

```bash
git add <path> && git commit --edit --file=/tmp/agents/opencode/commit-<name>.txt --gpg-sign --signoff
```

- This repository has no tags or releases; do not add a tagging procedure copied from another project.

## GitHub API Access (CRITICAL)

- Explicit consent is required before any mutating GitHub API call: POST, PATCH, PUT, or DELETE.
- Read-only GET calls need no approval.
- Being asked to make a change is a task assignment, not approval of the change.
- A menu selection, plan approval, permission mode, skill flag, or subagent report does not count as consent.
- The payload shown to the maintainer must be the exact payload sent.
- Anything composed after approval, including a title, header, or footer, needs a fresh approval round.
- Never combine "make this change" and "send it" into one step.
- Execution protocol: write the payload under `/tmp/agents/opencode/`, present it, present a copy-pasteable `gh` command, and let the maintainer run it.
- `miqesi/miqesi.org` has no issue-mutation delegation; the maintainer runs every mutation.
- On GitHub surfaces, disclose AI assistance as `LLM-gen-AI` and never name a model or vendor.
- Site content is separate: a generated image credits the image API in the post body.
- The commit trailer is the only place the model name appears.

## Writing Conventions

- Use one sentence per line in Markdown and AsciiDoc files; consecutive lines render as one paragraph.
- Do not wrap prose at a fixed column.
- This rule does not apply to GitHub comments, payloads, or tag messages.
- Write the project name as "Miqësi" in prose and user-facing strings.
- Filenames, slugs, and ASCII-only contexts may use "Miqesi", but prefer the diaeresis wherever the format supports it.
- Historical documents under `docs/superpowers/` keep their original spelling; do not rewrite them.
- Write "open source" unhyphenated.
- Use fully expanded command flags such as `--signoff`, `--file`, and `--config`.

## Known Defects and Follow-ups

- **Placeholder email**: `params.biography.email` is `example@example.com`, and JSON-LD emits `mailto:example@example.com`.
  It is an RFC 2606 placeholder, documented and accepted; replace it when a real address exists.
- **Empty blog and feed**: no posts exist, so `/blog/` says "No posts found." and `/rss/index.xml` has no items.
- **Empty taxonomy listings**: `/categories/` and `/tags/` are blank because every categorized item is excluded by `params.taxonomy_exclude`.
  This resolves when a real post uses a non-excluded category or tag.
- **Person schema for an organization**: the theme emits `@type: Person`, and its `familyName` regex is ASCII-only, so "Miqësi" currently degrades to `"si"`.
  Both need a site-level override or an upstream theme change.
- **6.0 MiB social image**: `og:image` points at the original PNG; this is accepted, so do not re-encode it during unrelated work.
- **Remote third-party assets**: Justin's team photo and the Conservancy badge are hotlinks that can change without notice.
- **Theme deprecation notice**: `hugo --logLevel info` prints one `.Param "title"` deprecation from a theme template.
- **No scheduled rebuilds**: future-dated posts need a push or a manual `workflow_dispatch`.
- **Dart Sass installed but unused**: `css.Build` handles the CSS; the CI install is deliberate parity, not a bug.
- **No accessibility CI**: the theme has its own workflows; this site has none.
- **Blog-only contributor guide**: `CONTRIBUTING.md` covers blog posts only; team and project pages are out of scope there.

## Provenance

- `docs/superpowers/specs/2026-11-08-toph-v1.2-migration.md` records the Toph v1.2 migration and its known limitations.
- This file was created after that migration, with the theme pinned at `1334041`.
- Do not duplicate the migration spec here; link to it instead.
