# CLAUDE.md

Personal blog built with Hugo + PaperMod, deployed to GitHub Pages via GitHub Actions.

## Overview

Write markdown in `content/posts/`, push to main, site deploys automatically. That's the whole workflow.

## Repo Structure

- `content/posts/` — blog posts (markdown with YAML frontmatter)
- `content/search/` — search page (PaperMod built-in)
- `hugo.toml` — site config (theme, params, menus, markup)
- `layouts/partials/extend_head.html` — KaTeX math support
- `archetypes/posts.md` — template for new posts
- `themes/PaperMod/` — theme (git submodule, don't edit directly)
- `.github/workflows/deploy.yml` — GitHub Actions deploy pipeline

## Writing Posts

Create a new post:
```bash
hugo new content posts/my-post-title.md
```

Or just create `content/posts/my-post-title.md` directly with frontmatter:
```yaml
---
title: "My Post Title"
date: 2026-04-26
draft: false
tags: ["data-engineering", "python"]
summary: "One-line summary for the post list."
math: true  # only if post uses KaTeX
---
```

**Draft workflow**: set `draft: true` while writing, flip to `false` when ready to publish. Drafts are not deployed.

## Tags

Use lowercase, hyphenated tags. Current categories:
- `data-engineering`, `python`, `sql`, `duckdb`
- `software-engineering`, `cli`, `system-design`
- `ai`, `agents`, `claude-code`, `llm`
- `quant`, `backtesting`, `trading`
- `meta`

## Local Preview

```bash
hugo server --buildDrafts
```

Site available at http://localhost:1313/. Live-reloads on file changes.

## Math Support

Posts with `math: true` in frontmatter get KaTeX. Use `$...$` for inline and `$$...$$` for display math.

## Workflow

- **Be proactive** about commits and pushes — pushing to main IS deploying.
- New posts should have clear, descriptive titles and accurate tags.
- Keep summaries concise — they appear on the post list page.
- Code blocks get automatic syntax highlighting and copy buttons.
- Images go in `static/images/` and reference as `/images/filename.png`.
