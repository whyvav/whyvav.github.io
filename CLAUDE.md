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
| Home / About page | `_pages/about.md` | plain prose, no special fields |
| CV (Markdown) | `_pages/cv.md` | plain Markdown sections |
| Sidebar profile | `_config.yml` → `author:` block | `avatar`, `bio`, `location`, `employer`, `email`, social links |
| Navigation bar | `_data/navigation.yml` | add/remove/reorder top-level links |
| Publications | `_publications/*.md` | `title`, `collection: publications`, `category` (`manuscripts`/`conferences`/`books`), `date`, `venue`, `paperurl`, `slidesurl`, `bibtexurl`, `citation` |
| Talks | `_talks/*.md` | `title`, `collection: talks`, `type` (`Talk`/`Tutorial`), `date`, `venue`, `location` |
| Teaching | `_teaching/*.md` | `title`, `collection: teaching`, `type`, `venue`, `date`, `location` |
| Portfolio | `_portfolio/*.md` | `title`, `collection: portfolio`, optional `header.image` |
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

Most collection files (`_publications/`, `_talks/`, `_teaching/`, `_portfolio/`, `_posts/`) are **template examples** from the upstream repo and should be replaced. `_pages/about.md` and `_pages/cv.md` are also still template text. The `_config.yml` `author:` block is partially filled (name, bio, location, academic/social links done).

## Deployment

Push to `main` → GitHub Actions builds the site → live at https://whyvav.github.io within ~1 minute. No manual deploy step.
