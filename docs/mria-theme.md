# Mria theme reference

This site is built on **Mria**, a paid Hugo blog theme by Anvod Studio (ThemeForest). The theme is vendored into the repo root (`layouts/`, `assets/`), not installed as a Hugo module or submodule.

These notes paraphrase the vendor's 1.1.2 documentation (released 4 Mar 2026) so nothing is lost if the download goes missing. They are written in our own words. The vendor docs are not redistributed here. **In this repo** callouts mark where this site differs from the vendor defaults.

- Vendor docs version: 1.1.2
- Version vendored here: 1.1.1 (header of `assets/sass/main.scss`). Upgrade tracked in #11.

## Requirements

- Hugo **extended** (the theme compiles SCSS).
- The theme ships as a full Hugo site, so there's nothing to install. Run `hugo server -D` in the project root.

> **In this repo:** The vendor recommends Netlify. This site deploys to GitHub Pages via `.github/workflows/hugo.yml` instead.

## Site config (`config.toml`)

Most behavior is controlled by `config.toml`. Each home-page section is a `[params.<name>]` block with an `enable` flag.

| Block | What it controls | Key settings |
|---|---|---|
| top level | Site basics | `baseURL`, `title`, `googleAnalytics` |
| `[pagination]` | Posts per page | `pagerSize` (Hugo ≥ 0.128; older Hugo used top-level `paginate`) |
| `[menu]` | Header and footer links | See [Navigation](#navigation) |
| `[params]` | Logo and color mode | `logo`, `logo_dark`, `description`, `color_scheme` = `auto` / `light` / `dark` |
| `[params.hero]` | Home hero | `hero__title`, `hero__description`, `hero__image` |
| `[params.blog]` | Home "latest posts" list | `blog__title`, `blog__description` |
| `[params.videos]` | Home "latest videos" list | `videos__title`, `videos__description` |
| `[params.tag]` | Home section for one tag | `tag` = the tag to feature |
| `[params.author_section]` | Home author card | `author__section__title`, `__description`, `__big__image`, `__small__image`, `__image__alt`, `aurhor__section__button__text` / `__link` |
| `[params.mailchimp]` | Newsletter signup | `mailchimp__identifier` (form URL), title, description, input/button text, background image |
| `[params.footer]` | Footer text | `footer__description` |
| `[[params.social]]` | Footer social icons, one block per link | `icon` (Font Awesome class), `name`, `link` |
| `[params.contact]` | Contact form | `form_id` (Formspree; the free tier allowed ~50 submissions/month at the time) |
| `[params.disqus]` | Comments | `enable`, `disqusShortname` |
| `[taxonomies]` | Taxonomies | `author = "authors"`, `tag = "tags"` |
| `[markup]` | Goldmark, TOC, highlighting | `unsafe = true` (raw HTML in Markdown), TOC levels 2–3, `noClasses = false` |
| `[outputs]` | Home outputs | `["HTML", "RSS", "JSON"]`. JSON feeds the search index |

> **In this repo:**
> - The vendor config enables `[params.videos]`. This site removed it, so `layouts/partials/section-videos.html` and `layouts/_default/videos.html` never render.
> - `[params.tag]` and `[params.mailchimp]` are disabled.
> - The `aurhor__` misspelling is the vendor's. `layouts/partials/section-author.html` reads the same spelling, so rename both or neither.
> - `baseURL` has a `/personal-blog/` subpath, so links and images in config and front matter repeat that prefix.

### Examples

Values come from this repo's `config.toml`, except `[params.videos]`, which comes from git history.

**Top level**

```toml
baseURL = "https://www.jsantosc.com/personal-blog/"
languageCode = "en-us"
title = "JSC"
googleAnalytics = ""
```

**`[pagination]`**

```toml
[pagination]
  pagerSize = 7
```

**`[menu]`** (one entry of each kind shown)

```toml
[menu]
  [[menu.main]]
    identifier = "blog"
    name = "Blog"
    url = "/posts/"
    weight = 2

  [[menu.footer]]
    identifier = "blog"
    name = "Blog"
    url = "/posts/"
    weight = 3
```

**`[params]`**

```toml
[params]
  logo = ""
  logo_dark = ""
  description = ""
  color_scheme = "auto"
```

**`[params.hero]`**

```toml
[params.hero]
  enable = true
  hero__title = "James Santos-Calacat"
  hero__description = "I assist software engineering teams as a Software and Site Reliability Engineer..."
  hero__image = ""
```

**`[params.blog]`**

```toml
[params.blog]
  enable = true
  blog__title = "Latest Posts"
  blog__description = ""
  blog__url = "/personal-blog/posts/"
```

**`[params.videos]`** (removed from this repo)

```toml
[params.videos]
  enable = true
  videos__title = "Latest Videos"
  videos__description = ""
```

**`[params.tag]`**

```toml
[params.tag]
  enable = false
  tag = "software-engineering"
```

**`[params.author_section]`**

```toml
[params.author_section]
  enable = true
  author__section__title = "Hello, my name is James. I’m a Site Reliability Engineer, Bodybuilder, and drummer."
  author__section__description = ""
  aurhor__section__button__text = "More About Me"
  aurhor__section__button__link = "/personal-blog/about/"
  author__section__big__image = "/personal-blog/images/profile-photo-03.jpg"
  author__section__small__image = "/personal-blog/images/profile-photo-04.jpg"
  author__section__image__alt = "James Santos-Calacat"
```

**`[params.mailchimp]`**

```toml
[params.mailchimp]
  enable = false
  mailchimp__title = "Get interesting news"
  mailchimp__description = "Subscribe to our newsletter and we’ll send you the emails of latest posts."
  mailchimp__identifier = ""
  mailchimp__input__text = "Your email address"
  mailchimp__button__text = "Subscribe"
  mailchimp__background__image = "https://placehold.co/1920x1080"
```

**`[params.footer]`**

```toml
[params.footer]
  footer__description = "You never know how strong you are, until being strong is your only choice"
```

**`[[params.social]]`**

```toml
[[params.social]]
icon = "fa-brands fa-linkedin"
name = "LinkedIn"
link = "https://www.linkedin.com/in/jsantosc/"
```

**`[params.contact]`**

```toml
[params.contact]
  form_id = "xeoaqnzw"
```

**`[params.disqus]`**

```toml
[params.disqus]
  enable = false
  disqusShortname = ""
```

**`[taxonomies]`**

```toml
[taxonomies]
  author = "authors"
  tag = "tags"
```

**`[markup]`**

```toml
[markup]
  [markup.goldmark]
    [markup.goldmark.renderer]
      unsafe = true

  [markup.tableOfContents]
    startLevel = 2
    ordered = false
    endLevel = 3

[markup.highlight]
  codeFences = true
  guessSyntax = true
  noClasses = false
```

**`[outputs]`**

```toml
[outputs]
  home = ["HTML", "RSS", "JSON"]
```

## Authors

1. Create `content/authors/<slug>/_index.md`. The leading underscore is required.
2. Give it front matter, with an optional bio as the body:

   | Key | Purpose |
   |---|---|
   | `name` | Display name |
   | `image` | Avatar |
   | `cover` | Cover image on the author page |
   | `location`, `website`, `twitter`, `facebook` | Shown on the author card or page |

3. Reference the slug in a post: `authors: [<slug>]`.

The vendor's default author is `admin`.

> **In this repo:** The only author is `jsantos`. Its `_index.md` sets `linkedin`, but no template reads that key, so it isn't displayed.

## Posts

Front matter keys the theme reads:

| Key | Effect |
|---|---|
| `title`, `description`, `date` | Standard |
| `authors` | List of author slugs |
| `image` | Hero image |
| `video_embed` | Embed URL (e.g. `https://www.youtube.com/embed/<id>`). Replaces the hero image with an iframe, which makes it a "video post" |
| `tags` | Tag list |
| `tags_color` | Hex color applied to this post's tags |
| `featured` | `true` adds the post to the home "featured" section, newest first |
| `toc` | `true` shows a table of contents generated from the headings |

Put images in `static/images/`.

**Wide images:** add `#wide` to the end of an image path in Markdown, e.g. `![alt](/personal-blog/images/foo.jpg#wide)`. The selector lives in `assets/sass/4-layouts/_post.scss`.

> **In this repo:**
> - The vendor docs say `_posts/` (a leftover from Jekyll). Posts live in `content/posts/YYYY-MM-DD-slug.md`.
> - Image paths must start with `/personal-blog/`.
> - The "darker green tags" change was made per post with `tags_color: '#5e8f2c'`, not in the theme's SCSS.

## Pages

Any Markdown file in `content/` becomes a page. Pages use only `title` and `image` front matter. `content/about.md` is an example.

## Projects

The vendor table of contents links to a Projects section, but the docs have no such section. The only trace is an image the docs never reference (`assets/img/project.png`), which shows a front matter example with `title`, `date`, `subtitle`, and `image`.

> **In this repo:** There's no projects layout or content, so this is unused.

## Colors

All colors are CSS custom properties in `assets/sass/0-settings/_color-scheme.scss`:

- `:root { ... }` sets light mode.
- `:root[dark] { ... }` sets dark mode.

Main variables:
- `--brand-color`, and a small palette (`--white`, `--light-gray`, `--light-blue`, `--blue-gray`, `--gray`, `--dark`, `--dark-blue`).
- Role variables built from that palette: background, text, heading, link, button, border, table rows, syntax-highlighting background.
- `--rgb-image-color` / `--image-gradient` for the overlay on images.

Change a role variable to change one element. Change a palette variable to change everything that uses it.

## Gallery

Raw HTML in a post or page (allowed because `unsafe = true`):

```html
<div class="gallery-box">
  <div class="gallery gallery-column-3">
    <img src="/personal-blog/images/a.jpg" loading="lazy">
    <img src="/personal-blog/images/b.jpg" loading="lazy">
  </div>
  <em>Optional caption</em>
</div>
```

The default is two columns. Add `gallery-column-3` or `gallery-column-4` for more. Styles: `assets/sass/3-modules/_gallery.scss`.

## Navigation

`[[menu.main]]` (header) and `[[menu.footer]]` entries each take `identifier`, `name`, `url`, and `weight` (sort order). For a dropdown:
- set `hasChildren = true` on the parent entry
- set `parent` on each child

`layouts/partials/header.html` renders the dropdowns.

> Hugo matches `parent` against the parent entry's `identifier`. The vendor example's parent has `identifier = "videos"` while its children use `parent = "Pages"`, so check that the two match before relying on it.

> **In this repo:** The menus are Home, Blog, Contact, and About. The vendor's Authors, Videos, and Elements entries were removed.

## Comments (Disqus)

Set `[params.disqus] enable = true` and `disqusShortname`. Comments render only when both are set (`layouts/_default/single.html`). If comments fail to load, register the site with Disqus first.

> **In this repo:** Disabled.

## Google Analytics

Set the top-level `googleAnalytics` value in `config.toml`. `layouts/_default/baseof.html` uses Hugo's built-in `_internal/google_analytics.html` template.

> **In this repo:** Empty, so nothing renders. Newer Hugo deprecates top-level `googleAnalytics` in favor of `[services.googleAnalytics] ID`. Revisit this during the Hugo upgrade (#11).

## Favicon

Replace `static/favicon.ico`.

## Third-party pieces bundled with the theme

- Google Fonts: Noto Sans Display, Noto Serif
- Font Awesome (icons; `assets/sass/0-settings/_fontawesome.scss` and friends)
- Vanilla Lazyload (lazy images)
- Lightense Images (click-to-zoom on post and page images)
- Reframe.js (responsive iframes; add the `reframe-off` class to opt out)
- An instant-search approach based on a public Hugo search tutorial, modified by the vendor (`layouts/_default/index.json` plus `assets/js/scripts.js`)

## Theme version history

| Version | Date | Notes |
|---|---|---|
| 1.1.1 | — | Version vendored here |
| 1.1.2 | 4 Mar 2026 | Supports Hugo's new template system. Renames: `index.html` → `home.html`, `terms.html` → `taxonomy.html`, `author.terms.html` → `authors/taxonomy.html`, `author.html` → `authors/term.html`. See #11 |

Vendor support goes through Anvod Studio's ThemeForest profile.
