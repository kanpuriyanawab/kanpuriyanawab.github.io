# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Personal website for Anshuman Mishra (heyyanshuman.com) — Machine Learning Engineer at Zomato. Built with Quarto as a static site with blog, project showcase, and personal notes.

## Build & Development Commands

```bash
quarto preview                              # Local dev server with live reload
quarto render                               # Full site build → _site/
quarto render posts/agent_memory_101.qmd    # Render a single page while iterating
```

Publishing is automated: pushes to `master` trigger `.github/workflows/publish.yml`, which runs Quarto and pushes the built site to the `gh-pages` branch. No need to run `quarto publish` manually.

## Hosting & Deployment

- **Hosting**: Cloudflare Pages project `kanpuriyanawab-github-io`, serving from the `gh-pages` branch
- **Custom domain**: `heyyanshuman.com` via Cloudflare CNAME → `kanpuriyanawab-github-io.pages.dev`
- **Flow**: push to `master` → GitHub Actions renders → pushes built HTML to `gh-pages` → Cloudflare Pages deploys
- GitHub Pages settings in the repo are irrelevant — ignore any warnings there

## Site Architecture

### Configuration & Theme
- **_quarto.yml**: Minimal site config — `theme: default` with `site.css` for all custom styling
- **_nav.html**: Custom fixed navigation bar, injected site-wide via `include-before-body`. Edit this to change nav links.
- **_styles.html**: Inline `<style>` block injected via `include-in-header`. **Must be kept in sync with `site.css`** — this is a workaround for a GitHub Pages/Cloudflare Pages bug where `site.css` was served as 0 bytes. Both files must always have the same CSS rules.
- The site hides Quarto's default navbar, sidebar, TOC, and color scheme toggle via CSS (`display: none !important`). All navigation comes from `_nav.html`.
- `site.css` defines a paper-toned, serif-based, narrow (600px) reading layout with CSS custom properties (`--paper`, `--ink`, `--muted`, `--rule`)

### Content Organization
- **index.qmd**: Home page (bio, experience, open source)
- **writing.qmd**: Unified listing page for all posts and notes (replaces former `tech-blog.qmd` / `my-notes.qmd`)
- **work.qmd**, **projects.qmd**, **talks.qmd**: Section pages at root level (previously under `pages/`, moved to root for clean routing)
- **posts/**: Technical blog posts (`.qmd` files, shared config in `posts/_metadata.yml`)
- **notes/**: Personal reflections (`.qmd` files, shared config in `notes/_metadata.yml`)
- **archive/**: Older interview content

### Blog Post Frontmatter Template
```yaml
---
aliases:
- /post_slug
permalink: /post_slug
badges: false
toc: true
categories:
- Category1
date: 'YYYY-MM-DD'
description: Short description
hide: false
output-file: filename.html
search_exclude: false
title: Blog Post Title
---
```

### Assets & Extensions
- Images: `assets/images/pfp/` (profile pics), `posts/assets/[slug]/`, `notes/assets/[slug]/`
- Extensions: `_extensions/quarto-ext/fontawesome`, `_extensions/schochastics/academicons`

## Known Gotchas

### Alias redirect loops on Cloudflare Pages
Quarto aliases generate redirect files at `_site/<alias>/index.html`. If an alias matches the page's own clean URL (e.g., alias `/work` on `work.qmd`), Cloudflare Pages can serve the redirect file for the `.html` URL, creating a loop: `/work.html` → `work/index.html` → `../work.html` → repeat. **Never add an alias that is just the stem of the page filename** (e.g., don't add alias `/work` to `work.qmd`). Legacy redirects like `/ml` or `/pages/work` are fine.

### Stale alias directories after removing an alias
Quarto does not clean up previously generated alias directories when an alias is removed. After removing an alias from frontmatter, manually delete the corresponding `_site/<alias>/` directory before pushing, or it will persist in the deployed site.

### Quarto version pinned in CI
`.github/workflows/publish.yml` pins Quarto to `1.7.31` to match local. If upgrading Quarto locally, update the workflow too.

## Git Workflow

- Main branch: `master`
- `_site/` is **not** tracked (ignored via `**/_site/**` in `.gitignore`)
- `.quarto/` build cache is ignored
- Commit style: short, lowercase, imperative subjects (e.g., `fix tag`, `quarterly update`)

## Naming Conventions

- Section pages: root-level simple names (`work.qmd`, `talks.qmd`)
- Blog posts: lowercase, underscore-separated slugs (`posts/agent_memory_101.qmd`)
- 2-space indentation in YAML
- Keep styling changes in both `site.css` and `_styles.html` (they must stay in sync)

## Important Context

- **Current Role**: ML Engineer at Zomato (AI evaluation platform and SDK called Gavel)
- **Newsletter**: The Conductor (conductorbyam.substack.com)
- **Google Developer Expert**: In Machine Learning (past)
- **Execution mode**: `freeze: auto` — computational code output is cached; posts inherit `freeze: true` from `_metadata.yml`
