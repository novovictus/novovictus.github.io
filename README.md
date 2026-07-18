# novovictus.github.io

Canonical site source for [ninja-neer.net](https://ninja-neer.net/).

This repository publishes the public engineering notes and project index for `novovictus` through GitHub Pages.

## Live site

- Canonical URL: https://ninja-neer.net/
- GitHub Pages source: `main`
- Custom domain: `ninja-neer.net`

## Repository layout

- `index.html`: landing page and project index
- `side-quests/*.html`: published standalone engineering notes
- `side-quests/*.md`: source work logs or supporting notes that are not automatically rendered as site pages
- `sitemap.xml`: manually maintained list of public HTML pages
- `.well-known/security.txt`: security contact metadata

The repository includes `.nojekyll`, so Markdown files are not converted into styled pages by GitHub Pages. Public articles should be published as HTML and linked from `index.html`.

When a public HTML page is added, renamed, or materially updated, update `sitemap.xml` in the same change.

## Site metadata

The site includes basic crawler and trust metadata:

- `CNAME`
- `robots.txt`
- `sitemap.xml`
- `.well-known/security.txt`
- `.nojekyll`
