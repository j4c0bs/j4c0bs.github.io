# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

Personal blog built with Hugo + PaperMod, deployed to GitHub Pages via GitHub Actions. Write markdown in `content/posts/`, push to main, site deploys automatically.

## Commands

```bash
hugo server --buildDrafts        # local preview at http://localhost:1313/
hugo new content posts/slug.md   # new post from archetype (starts as draft)
hugo --gc --minify               # production build to public/
```

## Writing Posts

Frontmatter (all fields required except `math`):
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

- `draft: true` while writing, flip to `false` when ready to publish. Drafts are excluded from production builds.
- Tags: lowercase, hyphenated. Existing: `data-engineering`, `python`, `sql`, `duckdb`, `software-engineering`, `cli`, `system-design`, `ai`, `agents`, `claude-code`, `llm`, `quant`, `backtesting`, `trading`, `meta`.
- Images go in `static/images/`, reference as `/images/filename.png`.

## Math Support

Posts with `math: true` get KaTeX via `layouts/partials/extend_head.html`. Use `$...$` for inline, `$$...$$` for display. Hugo's goldmark passthrough is configured in `hugo.toml` to preserve math delimiters.

## Key Constraints

- `themes/PaperMod/` is a git submodule — never edit files inside it. Override via `layouts/`.
- Pushing to main triggers GitHub Actions deploy. Treat every push as a production deploy.
- `hugo.toml` has `unsafe = true` in goldmark renderer (needed for raw HTML in posts).
- Syntax highlighting uses CSS classes (`noClasses = false`), not inline styles.
