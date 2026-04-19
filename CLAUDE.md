# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## About

Personal blog at [balaji.dev](https://balaji.dev) — Jekyll static site deployed to GitHub Pages via the `master` branch.

## Commands

```bash
# Install dependencies (first time)
bundle install

# Serve locally with live reload
bundle exec jekyll serve

# Build for production
bundle exec jekyll build
```

The site auto-deploys on push to `master` via `.github/workflows/jekyll.yml`.

## Post Types and Front Matter

There are two post types, distinguished by layout and tag:

### Microblog (short-form)
No body content — everything goes in the `gist` field. Used for short thoughts, reactions, links.

```yaml
---
layout: microblog
date: 2025-01-14T14:34:14+01:00
tags:
  - sometag
gist: >-
  Content here. HTML is allowed, e.g. <br><br> for paragraph breaks.
img:
  src: filename.jpg   # relative to /assets/img/
  width: 400
---
```

### Blog post (long-form)
Body content in Markdown below front matter. The `gist` field is used as a teaser on the home feed and should link to the post.

```yaml
---
layout: post
date: 2025-01-05T18:56:32+01:00
title: Post Title
categories:
  - software
tags:
  - blog        # IMPORTANT: must include "blog" tag for home feed styling
gist: >-
  <a href="/post-slug">Post Title</a>
img:
  src: filename.jpg
  width: 400
---

Post body in Markdown...
```

**The `blog` tag** controls home feed display: posts tagged `blog` get the `.blog` CSS class, `bookmark` gets `.bookmark`, everything else gets `.microblog`.

## File Naming

Posts go in `_posts/` with filename format: `YYYY-MM-DD-slug.md`. The permalink is `/:title` (the slug without the date).

## Architecture

- `_layouts/home.html` — home feed; renders `post.gist` (truncated to 700 chars) for all posts, paginated at 10
- `_layouts/microblog.html` — renders `page.gist` + optional image
- `_layouts/post.html` — renders full Markdown body
- `_layouts/default.html` — base layout wrapping all others
- `_includes/` — shared partials (head, header, footer, author)
- `_pages/` — static pages (about), output as collections
- `assets/img/` — images referenced in posts
- `_site/` — build output, not committed

Tags are archived at `/tags/:name/` via `jekyll-archives`.
