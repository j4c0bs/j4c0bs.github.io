---
name: new-post
description: >
  Collaborative workflow for writing and publishing Hugo blog posts. Use this skill whenever the user
  wants to write a new blog post, draft multiple posts, publish content to the blog, or says anything
  like "let's write a post about...", "I want to blog about...", "new post", or "publish a post".
  Also trigger when the user describes a topic they want to write about in the context of this blog repo.
---

# Blog Post Workflow

This skill guides a collaborative writing session for publishing posts to the Hugo + PaperMod blog.
The workflow moves through five phases: Branch, Draft, Preview, Refine, Publish. Each phase completes
before moving to the next, but the Refine phase loops until the user is satisfied.

## Phase 1: Branch

Create a feature branch so work stays off main until ready to deploy.

```
git checkout main && git pull origin main
git checkout -b post/<slug>
```

Derive `<slug>` from the post topic — lowercase, hyphenated, concise. For multiple posts on one theme,
use a shared slug (e.g., `post/duckdb-series`). Ask the user if the slug isn't obvious from context.

## Phase 2: Draft

Write the post file at `content/posts/<slug>.md`. Start with this frontmatter:

```yaml
---
title: "<Title>"
date: <today's date, YYYY-MM-DD>
draft: true
tags: []
summary: ""
math: false
---
```

Frontmatter guidance:
- **title**: Clear, descriptive. Avoid clickbait — this blog's voice is direct and technical.
- **date**: Today's date unless the user specifies otherwise.
- **draft**: Always start as `true`. Flipped to `false` only in the Publish phase.
- **tags**: Pick from the existing set in CLAUDE.md. Lowercase, hyphenated. Suggest tags and let the user confirm or adjust. New tags are fine if nothing existing fits — just flag it.
- **summary**: One sentence that works on the post list page. Write a draft summary and refine it during the review cycle.
- **math**: Set to `true` only if the post uses KaTeX expressions (`$...$` inline, `$$...$$` display).

Writing approach:
- The user is a staff data engineer. Match their voice — technical, concise, opinionated where warranted. No filler paragraphs, no "in this post we will discuss..." preamble.
- Ask the user how much they want to provide vs. how much they want you to draft. Some posts they'll dictate; others they'll give a topic and let you run with a first draft.
- Use fenced code blocks with language hints for syntax highlighting.
- For posts with mathematical content, use KaTeX notation and set `math: true`.
- Images go in `static/images/` and are referenced as `/images/filename.png`.

After writing the draft, commit it on the feature branch:
```
git add content/posts/<slug>.md
git commit -m "draft: <slug>"
```

If there are multiple posts, write and commit each one.

## Phase 3: Preview

Start the local Hugo dev server so the user can see the rendered post in their browser:

```bash
hugo server --buildDrafts
```

Run this in the background. Then tell the user:

> The dev server is running at **http://localhost:1313/**. Your post is at **http://localhost:1313/posts/<slug>/**. Take a look and let me know what you think — I'll make changes based on your feedback.

Wait for the user. Do not proceed to Phase 4 until they respond. The preview is theirs to evaluate.

## Phase 4: Refine

This is the iterative core. The user reviews the post in their browser and comes back with feedback.
Feedback can be anything — restructure a section, fix tone, add a code example, cut a paragraph,
change the title. Apply changes, commit, and the dev server live-reloads automatically.

Each round:
1. Apply the user's requested changes to the post file
2. Commit: `git add -A && git commit -m "revise: <short description>"`
3. The dev server live-reloads — tell the user to check the browser

Commits during this phase are working commits. They get squashed on merge in Phase 5, so don't
worry about keeping them clean — just capture progress.

Keep looping until the user signals they're happy. Phrases like "looks good", "ship it", "let's publish",
"ready to go", or "merge it" mean move to Phase 5.

## Phase 5: Publish

The user has approved the post. This phase deploys it to production.

1. **Flip the draft flag**: Change `draft: true` to `draft: false` in the post frontmatter
2. **Final commit**:
   ```
   git add -A && git commit -m "publish: <slug>"
   ```
3. **Squash merge to main** (collapses all draft/revise commits into one clean commit):
   ```
   git checkout main && git pull origin main
   git merge --squash post/<slug>
   git commit -m "post: <slug>"
   ```
4. **Push** (this triggers the GitHub Actions deploy):
   ```
   git push origin main
   ```
5. **Clean up the branch**:
   ```
   git branch -D post/<slug>
   ```

After pushing, tell the user the post is deploying and will be live at
`https://j4c0bs.github.io/posts/<slug>/` once the GitHub Actions workflow completes (usually ~1 minute).

If there were multiple posts, confirm the user wants to publish all of them before flipping draft flags.

## Handling multiple posts

When the user wants to write several posts in one session:
- Use a single feature branch for the batch
- Draft and commit each post individually in Phase 2
- Preview all of them together in Phase 3
- The user can give feedback on any post during Phase 4
- Publish them all at once in Phase 5, or selectively if the user prefers
