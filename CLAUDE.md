# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

Hugo static blog (SRE, bodybuilding, drumming), deployed to GitHub Pages. Rules live in @AGENTS.md. Deeper notes: `docs/CODEBASE-NOTES.md`. Theme features and config keys: `docs/mria-theme.md`.

## Commands

```
hugo server -D                           # local dev server, includes drafts
hugo --gc --minify                       # production build (same as CI) → public/
hugo new posts/YYYY-MM-DD-slug.md        # new post from archetypes/default.md
```

- No tests, lint, or package.json. The build is the only check.
- Needs Hugo **extended** (SCSS). CI pins 0.134.2 (`.github/workflows/hugo.yml`); newer local Hugo can pass where CI fails.
- CI builds and deploys on push to `main` only. PRs are not built, so run `hugo --gc --minify` locally before pushing.

## Architecture

No theme dir: the theme is vendored into the repo root (`layouts/`, `assets/`).

- `config.toml` drives everything: menus, and each home-page section toggled by `[params.<name>] enable`.
- Home: `layouts/index.html` stitches together `layouts/partials/section-*.html`.
- Shell: `layouts/_default/baseof.html` (head, header, footer, JS, mermaid from CDN).
- Posts: `layouts/_default/single.html`, branch on `.Section "posts"`. Author card looks up `content/authors/<id>/_index.md` via the `authors` front matter.
- CSS: `assets/sass/main.scss`, compiled and inlined in `layouts/partials/head.html`.
- Search: `layouts/_default/index.json` emits the index (home outputs include `JSON`); the minified `assets/js/scripts.js` fetches it at the hardcoded `/personal-blog/index.json`.
- Mermaid: ```` ```mermaid ```` fences render via `layouts/_default/_markup/render-codeblock-mermaid.html`.
- Contact form posts to Formspree (`[params.contact] form_id`).

## Posts

File: `content/posts/YYYY-MM-DD-slug.md`. Hero image: `static/images/<slug>-hero.jpg`.

The archetype is incomplete. Copy front matter from an existing post instead:

```yaml
title: ...
description: ...
date: 2025-09-29 08:00:00 -0700
authors: [jsantos]
image: "/personal-blog/images/<slug>-hero.jpg"
tags: [...]
featured: false   # true → home "featured" section
toc: true         # table of contents (h2–h3)
```

Optional: `video_embed` replaces the hero with an iframe.

## Gotchas

- `aurhor__section__button__*` in `config.toml` is misspelled, and `layouts/partials/section-author.html` reads the same spelling. Fix both or neither.
- `/public/` and `/resources/` are build output and gitignored.
