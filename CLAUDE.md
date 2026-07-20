# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The Jekyll site for the Community Informatics Lab (CIL) at George Mason University, served from
`master` via GitHub Pages at the custom domain in `CNAME` (`cil.cec.gmu.edu`, also reachable at
gmu-cil.github.io). There is no CI workflow — pushing to `master` publishes.

## Commands

```bash
bundle install
bundle exec jekyll serve      # local preview at http://127.0.0.1:4000
bundle exec jekyll build      # output to _site/ (gitignored)
```

No tests, no linter.

## Architecture

Almost all site content lives in **`_data/*.yml`**, not in the page files. The `.md` pages are
thin: front matter plus a one-line `{% include %}`. To add a publication, person, project, or news
item, edit the YAML — do not touch templates.

| Data file | Rendered by | Appears on |
|---|---|---|
| `people.yml` | `_includes/people.html` | `/people/` |
| `chronicles.yml` | `_includes/news.html` | home page News (newest first) |
| `publication.yml` | `_includes/publications.html` | `/publications/` — Refereed |
| `nonrefereed.yml` | same include | `/publications/` — Reports |
| `present.yml` | same include | `/publications/` — Presentations |
| `software.yml` | same include | `/publications/` — Software |
| `projects.yml` | `_includes/projects.html` | `/projects/` |

Ordering in every list is the file's own order, top to bottom = newest first. Adding an entry means
inserting it at the top of the file, not appending.

Two lists are **hardcoded HTML**, not data-driven: `_includes/alumni.html` and
`_includes/interns.html`. Moving someone from People to Alumni means deleting their block from
`_data/people.yml` and adding a `<div>` line to `alumni.html` (keeping the reverse-chronological
grouping already there).

### YAML entry conventions

- Colons inside a title or venue must be escaped as `&colon;` — a raw `:` breaks YAML parsing.
  Similarly `&num;`/`&sharp;` for `#` and `&amp;`, `&rarr;` where they appear.
- Fields are optional; templates guard with `{% if %}`. Common shape for a publication:
  `authors, year, title, venue, abb, suffix, type, link, note, note2`. Blank keys are left in place
  for consistency rather than removed.
- Author strings are plain text in `Last, F.` form, comma-separated — no per-author markup.
- Person photos go in `assets/members/` and are referenced as `/assets/members/<file>`; entries
  without an `image` fall back to `/assets/cil_grey.png`.
- `description` and similar fields may contain inline HTML (`<a href=... target="_blank">`), which
  renders as-is.

News items past the 30th get class `news-hidden` (a "show more" toggle), so ordering matters for
what is visible by default.

## Page-by-page

Each page is a stub in the repo root; the substance is in the include and the data file.

- **`index.markdown`** → `_layouts/home.html`. The only page with prose written directly in a
  layout: the lab blurb, the two `/assets/front1.png` + `front2.png` banner images, the News
  section (`_includes/news.html` over `chronicles.yml`), and the Join blurb. Edit the layout to
  change any of that copy.
- **`people.md`** → three stacked includes: `people.html` (data-driven from `people.yml`),
  then `alumni.html` and `interns.html` (both hardcoded HTML). Current members only in the YAML.
- **`publication.md`** → `publications.html`, which renders four data files in one page behind a
  `.pub-header` anchor nav: `publication.yml` (Refereed), `nonrefereed.yml` (Reports),
  `present.yml` (Presentations), `software.yml` (Software). Entries are numbered with
  `forloop.rindex`, so the count descends and stays stable as you add to the top.
- **`projects.md`** → `projects.html` over `projects.yml`. Entries carry `status` (`On-Going`,
  `Terminated (...)`) and `fund` badges plus a logo strip; `pub` cross-references publications by
  free-text citation ("Hsu et al., 2023"), not by ID.
- **`resources.md`** → `resources.html`, entirely hand-written prose (ASSIP/STIP/OSCAR programs,
  PhD applicant guidance, training resources). No data file.
- **`_posts/`** exists with two 2020 posts but no page lists them; the site is effectively not a
  blog. Don't add news as a post — add it to `chronicles.yml`.

## Styling

The Minima theme gem is overridden wholesale: local `_sass/minima.scss` plus `_sass/minima/{_base,
_layout,_syntax-highlighting}.scss` shadow the gem's equivalents. Nearly all CIL-specific CSS lives
in `_sass/minima/_base.scss` — that is the file to edit for component styling.

Principles the existing CSS follows:

- **Tokens over literals.** Colors, fonts, and spacing come from the variables at the top of
  `_sass/minima.scss` — `$brand-color: #fa8a36` (tangerine, the lab's identity color),
  `$brand-color-emph: #FF6100`, `$text-color: #2d2a2b`, `$grey-color`, `$spacing-unit: 20px`,
  `$content-width: 960px`, Manrope at `$base-font-size: 20px`. Reuse these rather than hardcoding
  hex or px.
- **Responsive via the `media-query` mixin** with `$on-palm: 600px` / `$on-laptop: 960px`, not
  ad-hoc `@media` blocks.
- **Fixed-box + `object-fit: cover` for photos.** `.people .profile-photo` is a hard 213×213 box
  with `overflow: hidden`, and `.profile-photo img` is 213×213 `object-fit: cover`. Any aspect
  ratio will therefore display without distortion, but off-center subjects get cropped — so member
  photos should be **square, roughly 600×600, face centered**. Existing files in `assets/members/`
  follow that.
- **Secondary text is `$small-font-size` + `$grey-color`** (see `.profile-text`, `.program`,
  `.pub .venue`) — the convention for de-emphasizing metadata against body copy.
- **Semantic class names scoped by section** (`.pub .abb`, `.project-title`, `.alumni-dissertation`)
  rather than utility classes. Inline SVG icons are used for small affordances (see the alumni
  badge/home-link in `alumni.html`) instead of an icon font.

`_includes/head.html` cache-busts `/assets/main.css` with `site.time`, so style edits show up
immediately without a hard refresh.

### Traps

- **Root `.md` files become pages, and Liquid runs over them.** GitHub Pages builds with
  `jekyll-optional-front-matter` enabled, so a markdown file at the repo root with no front matter
  is still rendered as a page — and any `{%` tag inside it is *executed*, not shown. Documenting
  Liquid literally in such a file fails the build with `Liquid syntax error`. CLAUDE.md is listed
  under `exclude:` in `_config.yml` for exactly this reason; keep it there, and add any new
  docs-only markdown to that list too.
- **The published site is built by GitHub Pages' own toolchain, not this Gemfile.** The Actions log
  shows `github-pages v232` / `jekyll v3.10.0`, while the Gemfile pins Jekyll 4.0. A local build
  passing is therefore not proof the Pages build will; check
  `gh run list` after pushing.

- **Bootstrap is dead code.** `_sass/_bootstrap.scss` and `_sass/bootstrap/` vendor Bootstrap
  3.3.6, and the Gemfile pulls the `bootstrap` 4.6.2 gem, but nothing imports either — `minima.scss`
  imports only the three `minima/*` partials. So the `container-fluid`, `row`, `col-md-6`, and
  `img-responsive` classes scattered through the includes are inert. Don't reach for a grid class
  expecting it to work; write the CSS in `_base.scss`.
- **Local builds may fail on bundler version.** `bundle exec jekyll build` can die with
  "Could not find 'bundler' (2.3.19)" against system Ruby 2.6. Fix with
  `gem install bundler:2.3.19` (or `bundle update --bundler`) before assuming the site is broken.
