# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this project is

A personal academic website for Vaibhav Shukla, built on the [Academic Pages](https://github.com/academicpages/academicpages.github.io) Jekyll template (itself a fork of Minimal Mistakes). Hosted at https://whyvav.github.io via GitHub Pages. Jekyll transforms Markdown/YAML/HTML source files into a static site on every `git push` to `main`.

## Running locally

**Native Ruby on Windows (PowerShell)** — Ruby 3.3 + DevKit is installed at `C:\Ruby33-x64\`.

First-time setup (already done):
```powershell
gem install bundler
bundle install
```

Start the dev server (run this every time):
```powershell
bundle exec jekyll serve -l -H localhost
```

Site is at http://localhost:4000. Stop with `Ctrl+C`.

> Changing `_config.yml` requires restarting the server; Markdown/HTML changes hot-reload automatically.

**Note**: `Gemfile` includes `tzinfo-data` and `wdm` (Windows-required gems). Do not remove them.

`_site/` (Jekyll's compiled output) is gitignored — GitHub Pages re-runs Jekyll on push, so committing `_site/` is never needed.

## Architecture — how content flows

The site separates **content** (what you write) from **presentation** (the theme machinery). As a rule, never touch `_layouts/`, `_includes/`, `_sass/`, or `assets/` unless you are intentionally restyling.

### Content you will actually edit

| What | Where | Key front-matter fields |
|---|---|---|
| Home / About page | `_pages/about.md` | plain prose + HTML notice block at top |
| CV (Markdown) | `_pages/cv.md` | plain Markdown sections |
| Sidebar profile | `_config.yml` → `author:` block | `avatar`, `bio`, `location`, `employer`, `email`, social links |
| Navigation bar | `_data/navigation.yml` | add/remove/reorder top-level links |
| Publications | `_publications/*.md` | `title`, `collection: publications`, `category` (`manuscripts`/`conferences`/`books`), `date`, `venue`, `paperurl`, `slidesurl`, `bibtexurl`, `citation` |
| Talks | `_talks/*.md` | `title`, `collection: talks`, `type` (`Talk`/`Tutorial`), `date`, `venue`, `location` |
| Teaching | `_teaching/*.md` | `title`, `collection: teaching`, `type`, `venue`, `date`, `location` |
| Projects | `_projects/*.md` | `title`, `collection: projects`, optional `header.image` |
| Blog posts | `_posts/YYYY-MM-DD-slug.md` | `title`, `date`, `tags`, `categories` |
| Downloadable files | `files/` | linked as `/files/filename.pdf` |
| Profile photo | `images/profile.jpg` | referenced in `_config.yml` `author.avatar` |

**File naming for dated collections**: always `YYYY-MM-DD-slug.md`. The date in the filename drives sort order in archive pages and on the CV.

### How the CV page works

`_pages/cv.md` contains hand-written Markdown sections (Education, Work, Skills, etc.) **plus** Liquid loops that pull from the collections automatically:

```liquid
{% for post in site.publications reversed %}{% include archive-single-cv.html %}{% endfor %}
{% for post in site.talks reversed %}{% include archive-single-talk-cv.html %}{% endfor %}
{% for post in site.teaching reversed %}{% include archive-single-cv.html %}{% endfor %}
```

So adding a file to `_publications/` automatically populates both the Publications page and the CV.

### Theme and visual settings

- **Theme variant**: set `site_theme` in `_config.yml`. Options: `default`, `air`, `sunrise`, `mint`, `dirt`, `contrast`. Each has automatic light/dark mode.
- **Dark/light toggle**: built into the masthead; no config needed.
- **Custom CSS**: add to `assets/css/main.scss` (imports `_sass/` partials; safe to append overrides at the bottom).

### Bulk content generation

`markdown_generator/` contains Jupyter notebooks that convert CSV/TSV spreadsheets into correctly-formatted Markdown files for publications and talks. Useful workflow when adding many entries at once.

## Current content state

The site is populated with Vaibhav's real academic/professional information. All upstream template examples have been replaced.

### What is filled in

- **`images/`** — contains only site assets: favicons, `profile.jpg`, `under_construction.gif`, and the `themes/` subfolder. Template placeholder images (`editing-talk.png`, `bio-photo*.jpg`, `500x300.png`) have been deleted.
- **`files/`** — contains `cv.pdf` (linked from the CV page). Template `bibtex1.bib` still present — can be deleted (see below).
- **`_pages/about.md`** — real bio (astrophysics focus, MSc FAU, current ML/VLM work). Has an "under construction" notice block with a GIF (`/images/under_construction.gif`) at the top.
- **`_pages/cv.md`** — full CV: Education, Work Experience, Selected Projects, Publications (liquid loop), Interests & Skills, Workshops & Events, MSc Courses, Awards, Talks (liquid loop), Teaching (liquid loop). Has a "Download PDF" button linking to `/files/cv.pdf`.
- **`_config.yml`** `author:` block — name, bio, location, employer, email, LinkedIn, GitHub (`whyvav`), arXiv, Google Scholar, ORCID filled in.
- **`_data/navigation.yml`** — Publications, Projects, Talks, Teaching, CV. Blog Posts and Guide links removed.
- **`_publications/`** — 3 real entries: VLM ISM detection (in prep, manuscripts), Colorism/EACL 2026 (conferences), Misinformation LLMs/CIKM 2025 (conferences).
- **`_talks/`** — 6 real entries: 2 Remeis/ECAP seminars (2023), MSc thesis defense (Feb 2025), junge AG Potsdam (Jun 2025), LASS/CIKM 2025 (Nov 2025), EACL SRW 2026 (Mar 2026).
- **`_teaching/`** — 1 entry: Remeis CCD Lab Tutor (Apr 2023 – Oct 2024).
- **`_projects/`** — 3 entries: MSc thesis (LMC SNRs), Ising model (MCMC), Visualizing Relativity (BSc).

### What still needs to be done / open items

- **`_posts/`** — 5 template blog post files remain (`2012-...`, `2013-...`, `2014-...`, `2199-...`). Kept intentionally — blog posts may be added later. To re-enable the Blog Posts section in the nav, add it back to `_data/navigation.yml`.
- **`files/bibtex1.bib`** — template artifact (fake example entry), nothing links to it. Delete it. For real per-publication BibTeX, add individual `.bib` files to `files/` and set `bibtexurl: /files/yourpaper.bib` in the publication's front matter.
- **CV PDF** — `files/cv.pdf` should be kept up to date. The CV page links to it.
- **`markdown_generator/`** — template Jupyter notebooks, no longer needed for routine edits.

## Deployment

Push to `main` → GitHub Actions builds the site → live at https://whyvav.github.io within ~1 minute. No manual deploy step.
