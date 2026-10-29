# AGENTS.md

Hugo blog deployed to GitHub Pages. Build: `hugo --gc --minify`.

## Rules

- Front matter `image:` paths start with `/personal-blog/`. (Inferred)
- Don't edit `assets/js/scripts.js`. It is vendored and minified. (Inferred)
- Keep `baseURL` (`config.toml:3`) in sync with the hardcoded `/personal-blog/` paths in `scripts.js` (search URL), `config.toml` links and post front matter. (Inferred)
