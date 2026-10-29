---
created: 2026-10-27
updated: 2026-10-27
repo: personal-blog
source_commit: d39c26b
source_branch: main
source_date: 2025-09-29
flow_traced: publish a post
generated_by: code-onboard
status: current
tags: [codebase, onboarding]
---

# personal-blog: codebase notes

Hugo static site (personal SRE / bodybuilding / drumming blog) deployed to GitHub Pages. 94 tracked files, 6 posts, no tests. Layouts and assets live in the repo root (no separate theme dir).

Staleness check: `git rev-list --count d39c26b..HEAD`.

## 0 · Perimeter

- **Entry points:** `layouts/index.html:1` (home, 6 section partials), `layouts/_default/single.html:1` (posts), `layouts/_default/index.json:1` (search data, enabled by `home = ["HTML","RSS","JSON"]` in `config.toml`). No server, queue or cron.
- **Commands that run:** CI only, `hugo --gc --minify` at `.github/workflows/hugo.yml:56-58`, on push to `main` (`:7`) or manual dispatch (`:10`). No `package.json` or Makefile. README documents no commands.
- **Config:** `config.toml` (`baseURL` at `:3`), post front matter, `HUGO_ENVIRONMENT=production` in CI (`hugo.yml:54`). No env vars or secrets.
- **External boundaries:** Out: Formspree POST (`layouts/_default/contact.html:46`), Google Fonts (`layouts/partials/head.html`), mermaid from jsDelivr (`layouts/_default/baseof.html`), GitHub Pages deploy (`hugo.yml:60-62`). In: none.

## 1 · Architecture

```
content/*.md ──► layouts/_default/{single,list,terms}.html ──► partials/* ──► public/ ──► Pages
                          ▲                                        ▲
       config.toml [params.*] toggles               assets/sass/main.scss → inline <style> (head.html)
```

- Posts: `content/posts/*.md`. Read by `layouts/partials/section-blog.html:18` (first 7) and `index.json:2`.
- Author data: `content/authors/jsantos/_index.md`, read via `GetPage "taxonomyTerm"` (`single.html:29`).
- Images: `static/images/*`.
- Search: client fetches `/personal-blog/index.json`; path hardcoded in minified `assets/js/scripts.js`.
- CSS: SCSS compiled and inlined in `head.html` (`resources.Get "sass/main.scss" | toCSS`). Needs Hugo extended.
- Inert: `section-videos.html:1` reads `.Site.Params.videos.enable` but no `[params.videos]` exists. `[params.tag]` is `enable = false`. `featured: true` is read at `section-featured-posts.html:5`.

## 2 · Read first

| # | File | Why |
|---|---|---|
| 1 | `config.toml` | Every site toggle and menu. |
| 2 | `layouts/_default/baseof.html` | The shell: head, header, footer, JS, mermaid. |
| 3 | `layouts/_default/single.html` | Post rendering, author lookup, `toc`/`video_embed` switches. |
| 4 | `content/posts/2025-09-29-git-rebase.md` | Front matter contract. |
| 5 | `.github/workflows/hugo.yml` | How a merge becomes the live site. |

## 3 · Run, test, debug (all not run)

```
hugo server -D                          # not run. Inferred: standard dev server
hugo --gc --minify                      # not run. From hugo.yml:56
hugo new posts/2025-10-27-my-title.md   # not run. Uses archetypes/default.md
```

- Local Hugo is v0.157.0+extended; CI pins 0.134.2 (`hugo.yml:34`).
- `baseURL` has a `/personal-blog/` subpath (`config.toml:3`). Front matter images, the search URL and several config links hardcode it.
- `.gitignore` has inline comments on pattern lines (don't work in gitignore) and a `/github/workflows/` pattern missing the leading dot.
- Running `baseURL` value: unknown (no deploy override seen in the workflow).

## 3b · Change harness

Tests, lint, format, typecheck: none. Only gate is the build (`hugo --gc --minify`), run by CI on push to `main` only (`hugo.yml:6-7`); PRs are not built. `.github/CODEOWNERS:1` is `* @jdot-santos`. No pre-commit hooks found.

Landing zones: routes derive from `content/`; new home section = new `partials/section-*.html` called from `layouts/index.html:3-11`; menu in `config.toml` `[[menu.main]]`; toggles are `enable = ...` under `[params.*]`; migrations, DI: none.

## 4 · Flows

- Publish post: add `content/posts/*.md`, push to `main`, CI builds and deploys.
- Search: browser fetches `index.json`, filters client-side.
- Contact form: browser POSTs to Formspree. Nothing stored.
- Theme toggle: `localStorage` decides dark mode (`head.html`); toggle itself not traced.

No cron/queue flows.

### Trace: publish a post

1. `hugo new` uses `archetypes/default.md:1-6` (title, date, `image: "/images/"`, empty tags). No `authors`, `description`, `toc`, `featured`.
2. Real posts add those by hand (`content/posts/2025-09-29-git-rebase.md:2-10`).
3. Surprise: archetype image has no `/personal-blog/` prefix; real posts use it (`git-rebase.md:6`). Layouts apply `absURL` (`single.html:75`, `section-blog.html:34`); author image (`single.html:31`) does not.
4. Push to `main` triggers the workflow (`hugo.yml:7`), builds with Hugo 0.134.2 (`:34`), uploads `./public` (`:62`), deploys.
5. `content/posts/_index.md` renders through `list.html:32`, paginated via `where ... "Type" "posts"` (`:53`), no explicit sort. Inferred: Hugo default order (weight, then newest date).
6. Post page renders via `single.html:3`, branch `eq .Section "posts"`. Other sections fall to the generic branch (`:168`) with no author card or share buttons.
7. Optional features: `toc: true` (`single.html:87`), `video_embed` replaces hero with iframe (`:65`), `mermaid` fence calls `render-codeblock-mermaid.html:4` and loads mermaid from CDN.
8. Home shows 7 newest posts (`section-blog.html:18`); featured list is separate (`section-featured-posts.html:5`).
9. Search index picks it up automatically (`index.json:2-3`).
10. Related posts use tag intersection over `.Site.Pages` (`related-posts.html:1`), which includes non-post pages.

Silent failures: author typo hides author (`with` at `single.html:29`); missing `image` renders no hero; the `gt .Params.authors N` slice-vs-number comparison (`single.html:40`) is unverified; no PR build, so template errors surface only after merge.

## 5 · Conventions

- Posts: `content/posts/YYYY-MM-DD-slug.md`; images `static/images/<slug>-hero.jpg`.
- Home sections: `layouts/partials/section-*.html`, each guarded by `[params.<name>] enable`.
- BEM-ish CSS; SCSS split `0-settings` to `4-layouts`.
- Author/tag markup is duplicated across `single.html`, `section-blog.html`, `article.html`; looks inherited from a theme, not deliberate.
- `aurhor__section__button__*` misspelling in config (Inferred: template reads the same spelling; `section-author.html` not opened). Don't fix one side only.
- ADRs/specs: none.
- Rules an agent couldn't derive (all `Inferred:`):
  - Front matter image paths must start with `/personal-blog/`.
  - Don't edit vendored `assets/js/scripts.js`.
  - Keep `baseURL` and hardcoded `/personal-blog/` paths in sync.

## 6 · Riskiest parts

1. Hardcoded `/personal-blog/` paths (`scripts.js`, `config.toml`, post `image:` fields). Changing `baseURL` breaks search and images silently.
2. Hugo version drift (0.134.2 CI vs 0.157.0 local); `.Scratch` (`index.json:1`) is deprecated in newer Hugo.
3. No PR build gate.
4. `section-videos.html` / videos layout reference unconfigured params. Inert today.
5. `.gitignore` malformed patterns.
6. Vendored minified JS with hardcoded search URL.
7. Stale-but-imported check: not run.

## 7 · First change

Make `archetypes/default.md` match real posts: add `description`, `authors: [jsantos]`, `image: "/personal-blog/images/"`, `toc: true`, `featured: false`. Proof: `hugo new posts/test.md`, diff against `git-rebase.md:1-11`, delete it. Teaches the front matter contract and path convention.

## 8 · Unknowns and owners

Owner: `@jdot-santos` (`.github/CODEOWNERS:1`); shortlog shows one person (23 and 10 commits under two name spellings). No ADR folder.

1. Is `/personal-blog/` a permanent subpath, or will the site move to the domain root?
2. ~~Is `[params.videos]` intentionally missing?~~ Answered: the vendor config enables it and this site removed it (`docs/mria-theme.md`).
3. Should PRs get a `hugo` build check before merge?
4. Is the Hugo pin (0.134.2) deliberate, or can CI use 0.157.0?
5. Does `gt .Params.authors N` (`single.html:40`) behave as intended with one author?
6. Are `layouts/_default/videos.html` and `author.terms.html` used by anything?
