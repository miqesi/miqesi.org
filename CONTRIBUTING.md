# Contributing a blog post

This guide covers writing and publishing a blog post on miqesi.org.
Team profiles and project pages are out of scope for now and will be documented separately.


## Before you start

You need:

- Hugo `0.169.0` or later, extended edition.
- [Asciidoctor](https://asciidoctor.org) if you plan to write in AsciiDoc.
- Network access during the build, because the theme fetches fonts at build time.

Initialize the theme submodule once after cloning:

```bash
git submodule update --init --recursive
```

The Development section of `README.md` covers the rest of the toolchain.


## Create the post

Run this from the repository root:

```bash
hugo new content blog/2026/11/my-post-title.en.md
```

Change the year, month, and filename to match the post.
The command creates `content/blog/2026/11/my-post-title.en.md` and fills in the front matter from the theme's `archetypes/blog.md`.

The path decides the URL:

| File | Published URL |
|---|---|
| `content/blog/2026/11/my-post-title.en.md` | `/blog/2026/11/my-post-title/` |

Follow these naming rules:

- Use lowercase letters, digits, and hyphens.
- Do not use spaces or underscores.
- Keep the `.en.md` suffix for English content.
- Match the `YYYY/MM` folders to the publication date, as jwheel.org does.

Hugo groups the blog archive by the `date` field, not by the folders.
The folders only affect the URL.

The command title-cases the filename, so open the new file and fix the `title` field.


## Front matter

A Markdown post starts with this front matter:

```yaml
---
title: "My Post Title"
date: 2026-11-08T09:00:00-05:00
draft: true
description: ""
author: ""
categories: []
tags: []
images: []
toc: false
---
```

### Field reference

| Field | Required | What it does |
|---|---|---|
| `title` | yes | Page heading, archive entry, Open Graph headline, and Article structured data. Plain text only. |
| `date` | yes | Publication date with a timezone offset. The archetype inserts the current time, so adjust it to the planned date. Drives archive grouping and sort order. |
| `draft` | yes | When `true`, the post is excluded from builds. Add `-D` to `hugo server` to preview it. |
| `description` | strongly recommended | Plain-text meta description, social card summary, archive excerpt, and Article schema. Keep it under about 160 characters. |
| `author` | optional | Byline for the post and Article schema. Falls back to the site name, `Miqësi`. |
| `categories` | optional | Broad groupings such as `community` or `events`. Avoid `footer`, `projects`, and `team`. |
| `tags` | optional | Finer topics such as `accessibility` or `conferences`. Use lowercase and hyphens. |
| `images` | optional | Cover image, Open Graph and Twitter card, Article schema image, and RSS enclosure. Only the first entry is used. |
| `toc` | optional | Adds a table of contents to a Markdown post. AsciiDoc uses the `:toc:` attribute instead. |

The archetype does not include a few advanced fields:

- `slug` overrides the URL segment when the filename is not the one you want.
- `aliases` adds redirects from old URLs.
- `lastmod` normally comes from git history, because `enableGitInfo: true` is set.

Do not set `hide_sitemap: true` on a post.
The blog archive filters those pages out, so the post disappears from `/blog/` even though its URL still works.

Do not set `weight` on a post.
Posts are ordered by `date`.


## Cover images

Put the image in the same directory as the post:

```text
content/blog/2026/11/
├── my-post-title.en.md
└── my-post-title.jpg
```

Reference it by filename:

```yaml
images: ["my-post-title.jpg"]
```

Three path forms are accepted:

- A bare filename resolves relative to the post's directory.
- An absolute path such as `/pages/example.png` resolves from the published site root.
- A full URL such as `https://example.com/photo.jpg` is used as-is.

Only the first entry in `images` is used, even if you list more.

Practical guidance:

- Use `1200x630` when the image doubles as a social card, or `1600x900` for a wider cover.
- Keep the file under about 500 KB.
- Prefer JPEG or WebP for photographs and PNG for diagrams.
- Compress before committing, because Hugo does not resize images stored next to content.
- Remote images work as covers, but they are skipped as RSS enclosures because Hugo cannot read their size.


## Write the body

Start headings at `##`, because the title is already the `#` heading.
Put each sentence on its own line.
This keeps diffs small when someone edits a paragraph later.

Use root-relative paths for internal links, such as `/blog/2026/11/another-post/`.
The theme rebases them against `baseURL`, so they work in previews and production.
Use full URLs for external links.

Add an image with alt text and an optional caption:

```markdown
![A descriptive alt text](diagram.png "Optional caption shown under the image")
```

The alt text is required.
The caption is optional.

Raw HTML is not rendered in Markdown posts.
Use Markdown syntax instead of `<figure>` or `<div>`.

Heading anchors are added automatically, so readers can link to a section.


## AsciiDoc alternative

Use AsciiDoc when you need admonitions, definition lists, or other features that Markdown handles awkwardly.
Create the post with an `.adoc` extension:

```bash
hugo new content blog/2026/11/my-post-title.en.adoc
```

Hugo scaffolds this from the theme's `archetypes/blog.adoc`, which carries the same front matter as the Markdown archetype, minus `toc`.

Add document attributes right after the front matter:

```asciidoc
---
title: "My Post Title"
date: 2026-11-08T09:00:00-05:00
draft: true
description: "One or two sentences that explain what the post covers."
author: "Your Name"
categories:
  - "community"
tags:
  - "accessibility"
images:
  - "my-post-title.jpg"
---

:toc:

Opening paragraph.

== First section

Body text.
```

The site sets `markup.asciidocExt.preserveTOC: true`, so `:toc:` renders a styled table of contents.

Common AsciiDoc syntax:

- Internal links use `link:/blog/2026/11/another-post/[another post]`.
- Images use `image::diagram.png[Alt text,400,300]`.
- Admonitions use `NOTE:`, `TIP:`, `WARNING:`, and `IMPORTANT:`, and the theme styles them.

The [jwheel.org blog](https://jwheel.org/blog/) is a live example of the same theme with AsciiDoc posts, cover images, and tag pages.


## Categories and tags

Reuse existing terms when they fit.
Browse `/categories/` and `/tags/` on the live site before inventing a new one.

A new term is created the first time a post uses it.
You do not need to create files for tags or categories unless you want to customize their term page.

To give a category a description and an illustration, create `content/categories/<slug>/_index.md`:

```markdown
---
title: Accessibility
image: /pages/example.png
---

Posts about accessibility practice, tooling, and community work.
```

The categories page then shows the image, the description, and the most recent posts in that category.
Use `logo` instead of `image` for a small logo.
The path resolves from the site root, so put the file under `assets/pages/` and reference `/pages/<name>`, or under `static/` and reference `/<name>`.

Tags do not need term pages.
The tag cloud builds itself from post front matter.


## Preview and check

Run the development server, adding `-D` to include drafts:

```bash
hugo server -D
```

Check the following:

- The post appears under the right year and month at `/blog/`.
- The post URL matches the path you chose.
- The cover image appears at the top of the post.
- The category and tag badges link to term pages.
- The post appears in the homepage recent posts once it is among the newest.
- `/rss/index.xml` includes the post, with an `<enclosure>` element when the cover is a local file.

Run the production build before you commit:

```bash
hugo --gc --minify
```

The build must exit `0` with no `WARN` or `ERROR` lines.
A post with `draft: true` will not appear in `public/`, which is expected.
Set `draft: false` before you open the pull request.


## Commit and pull request

Use a short gitmoji-style subject:

```text
✨ blog: Add my post title
```

Keep the subject under 50 characters and explain the change in the body.
Sign off your commit:

```bash
git commit --signoff
```

Push your branch and open a pull request.
Every push to `main` deploys to production, so do not merge your own changes unless you are a maintainer.

If you used an AI assistant to write the post, add an `Assisted-by:` trailer that names the model and version.


## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| Post missing from the production build | `draft: true` or a future `date` | Set `draft: false` and check the date |
| Post appears under the wrong month | `date` differs from the folder | Update `date`, or move the file and rename it |
| Cover image returns 404 | Filename mismatch or wrong directory | Keep the image next to the post and match the spelling exactly |
| Cover missing from RSS | Remote URL, or the file is not next to the post | Use a local file so Hugo can read its size |
| Post missing from `/blog/` but the URL works | `hide_sitemap: true` is set | Remove it |
| Tag or category missing | Empty list, or the term is excluded | Check the front matter and `params.taxonomy_exclude` |
| Body shows literal HTML tags | The Markdown renderer leaves raw HTML disabled | Use Markdown, or switch to AsciiDoc |


## Full Markdown template

```markdown
---
title: "My Post Title"
date: 2026-11-08T09:00:00-05:00
draft: true
description: "One or two sentences that explain what the post covers."
author: "Your Name"
categories:
  - "community"
tags:
  - "accessibility"
  - "conferences"
images:
  - "my-post-title.jpg"
toc: true
---

Opening paragraph.
Another sentence on its own line.

## First section

Body text.

![Alt text for the image](diagram.png "Caption text")
```


## Full AsciiDoc template

```asciidoc
---
title: "My Post Title"
date: 2026-11-08T09:00:00-05:00
draft: true
description: "One or two sentences that explain what the post covers."
author: "Your Name"
categories:
  - "community"
tags:
  - "accessibility"
  - "conferences"
images:
  - "my-post-title.jpg"
---

:toc:

Opening paragraph.
Another sentence on its own line.

== First section

Body text.

image::diagram.png[Alt text,400,300]
```
