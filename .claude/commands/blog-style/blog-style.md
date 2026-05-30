---
name: blog-style
description: >
  Style guide and HTML/CSS element reference for the j4c0bs blog. Provides a design system
  (color palette, typography, spacing) and ~50 reusable components (cards, callouts, timelines,
  diagrams, diffs, badges, interactive elements) that can be embedded in Hugo/PaperMod blog posts
  via raw HTML. Use this skill whenever writing a new blog post and considering visual elements,
  enhancing an existing post with richer formatting, asking what design elements are available,
  or when the user mentions "style guide", "blog elements", "add a callout", "add a diagram",
  "rich formatting", "HTML components", or any request to make a post more visually structured.
  Also trigger when the /new-post skill is active and the post would benefit from visual elements
  beyond plain markdown.
---

# Blog Style Guide & Element Reference

This skill provides a design system and reusable HTML/CSS components for the j4c0bs blog (Hugo + PaperMod). Elements are embedded as raw HTML in markdown posts — Hugo's goldmark renderer has `unsafe = true` enabled.

## When to use

- Writing a new post that needs visual structure beyond plain markdown
- Adding callouts, comparison tables, timelines, diagrams, or other rich elements to existing posts
- Checking what design components are available
- Ensuring visual consistency across posts

## Design System

### Color Palette

```css
:root {
  /* Primary accent */
  --accent:             #5B5FC7;  /* electric indigo */
  --accent-light:       #EEEEFF;  /* callout backgrounds, light mode tints */
  --accent-dark:        #3D4096;  /* dark mode hover, pressed states */
  --accent-translucent: rgba(91,95,199,0.10);  /* highlights, term underlines */
  --accent-ring:        rgba(91,95,199,0.15);  /* focus rings */

  /* Neutrals */
  --ivory:    #FAF9F5;  /* page background */
  --slate:    #141413;  /* headings, primary text */
  --oat:      #E3DACC;  /* warm neutral surface */
  --white:    #FFFFFF;  /* card surfaces */
  --gray-100: #F0EEE6;  /* subtle backgrounds */
  --gray-300: #D1CFC5;  /* borders */
  --gray-500: #87867F;  /* secondary text, labels */
  --gray-700: #3D3D3A;  /* body text */

  /* Semantic */
  --olive:   #788C5D;  /* success, positive */
  --rust:    #B04A3F;  /* error, danger, deletions */
  --warning: #C78E3F;  /* caution */
  --info:    #5C7CA3;  /* informational */
}
```

### Typography

Three font stacks — serif for headings, sans for body, mono for code/labels:

```css
--serif: ui-serif, Georgia, "Times New Roman", serif;
--sans:  system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
--mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;
```

| Role | Family | Size | Weight |
|---|---|---|---|
| H1 | serif | 32-38px | 500 |
| H2 | serif | 22-26px | 500 |
| H3 | serif | 19px | 500 |
| Body | sans | 15px | 400 |
| Eyebrow/label | mono | 11-12px | 400, uppercase, `letter-spacing: 0.08em` |
| Code inline | mono | 13px | — |
| Small/meta | sans | 13px | 400 |

### Spacing

4px base unit. Scale: 4, 8, 12, 16, 20, 24, 32, 48, 64px.

### Border Radius

| Use | Value |
|---|---|
| Inline code, small chips | 4px |
| Buttons, badges, rows | 8px |
| Cards, panels | 12px |
| Large containers | 20px |

### Borders & Shadows

- Standard border: `1.5px solid var(--gray-300)`
- Accent left-border: `4px solid var(--accent)`
- Shadow (subtle): `0 2px 8px rgba(20,20,19,0.06)`
- Shadow (elevated): `0 4px 14px rgba(20,20,19,0.08)`
- Shadow (prominent): `0 8px 24px rgba(20,20,19,0.12)`

## Embedding in Hugo Posts

### CSS Scoping

Wrap all custom HTML in a scoped container to avoid style leaks between posts. Use a unique class per post based on the slug:

```html
<div class="rich-post-<slug>">
<style>
  .rich-post-<slug> {
    /* your element styles here, scoped to this post */
  }
</style>

<!-- your elements here -->
</div>
```

For elements used across multiple posts, define a shared `.j4-` prefix class and put the CSS in `assets/css/extended/` — PaperMod automatically loads CSS files from that directory.

### Dark Mode

PaperMod toggles a `.dark` class on the `<body>`. Override element styles for dark mode:

```css
.dark .your-element {
  background: var(--slate);
  color: var(--ivory);
  border-color: var(--gray-700);
}
```

At minimum, elements should not break in dark mode (white text on white background, etc.). The accent color (#5B5FC7) has sufficient contrast on both light and dark backgrounds.

### Base Reset

Include once at the top of any post using custom elements:

```css
.rich-post-<slug> * { margin: 0; padding: 0; box-sizing: border-box; }
.rich-post-<slug> { font-family: var(--sans); line-height: 1.55; }
```

## Element Index

Read the relevant resource file when you need the complete code for an element. Each file is self-contained with copy-paste-ready HTML+CSS.

### Layout Elements
**File:** `resources/layouts.md`

| Element | Description |
|---|---|
| Two-column with left nav | Sticky sidebar navigation + main content area |
| Two-column with right aside | Main content + sticky glossary/reference aside |
| Before/after comparison grid | Side-by-side panels for comparing approaches |

### Typography & Text Elements
**File:** `resources/text-elements.md`

| Element | Description |
|---|---|
| Eyebrow label | Monospace uppercase category tag above a heading |
| TL;DR callout | Prominent summary block with accent left border |
| Info/callout box | Tinted background box for notes and highlights |
| Warning/gotchas panel | Accent-bordered alert for caveats and pitfalls |
| Prompt/context box | Gray background box for setup context or prerequisites |

### Cards
**File:** `resources/cards.md`

| Element | Description |
|---|---|
| Flat card | No border, subtle background |
| Outlined card | Standard bordered card |
| Elevated card | Card with box-shadow |
| Accent-stripe card | Left accent border highlight |
| Inset card | Recessed appearance |
| Horizontal card | Side-by-side image + content |
| Summary stats grid | 4-column responsive stat cards |
| Comparison card grid | Multi-card layout for comparing approaches |

### Code Display
**File:** `resources/code-display.md`

| Element | Description |
|---|---|
| Diff block | Unified diff with add/del/context line styling |
| Dark code panel | Syntax-highlighted code on dark background |
| Tabbed code viewer | Multiple code files with tab switching |
| Collapsible accordion | Expandable sections for long code samples |
| Inline diff | Strikethrough + insertion for narrative-style diffs |

### Data Display
**File:** `resources/data-display.md`

| Element | Description |
|---|---|
| Status badges | Colored pills (success, warning, error, info) |
| Severity/risk badges | HIGH/MED/LOW indicators with colored backgrounds |
| Metric cards | Key-value stat display with optional delta arrows |
| Comparison table | Good/bad cell states with colored indicators |
| Progress indicators | Horizontal progress bars and step indicators |
| Risk dot | Inline colored dot for severity levels |

### Navigation
**File:** `resources/navigation.md`

| Element | Description |
|---|---|
| Sticky sidebar TOC | Fixed table of contents with scroll tracking |
| Collapsible details/summary | Native HTML5 accordion with styled chevron |
| Tab interface | Horizontal tab bar with content panels |
| Phase/rollout steps | Horizontal step strip for multi-stage processes |

### Timelines
**File:** `resources/timelines.md`

| Element | Description |
|---|---|
| Vertical milestone timeline | Dot + connector line with milestone cards |
| Incident timeline | Colored state dots with timestamp labels |
| Sequence timeline | Horizontal keyframe-style animation timeline |

### Diagrams & SVG
**File:** `resources/diagrams.md`

| Element | Description |
|---|---|
| SVG architecture diagram | Boxes + arrows flow diagram |
| Flowchart with nodes | Interactive node diagram with click-to-detail |
| Figure container | Bordered card wrapper for SVG with caption |
| SVG illustrations | Queue, retry-backoff, fan-out/fan-in patterns |
| Arrow markers | Reusable SVG arrowhead definitions |

### Interactive Elements
**File:** `resources/interactive.md`

| Element | Description |
|---|---|
| Task completion animation | Sequenced micro-interaction (check, strikethrough, confetti) |
| Checkbox checklist | Interactive done/pending list with custom styling |
| Drag-to-reorder | Native HTML5 drag-and-drop with visual indicators |
| Easing switcher | Control panel to demo animation curves |

### Reports & Structured Data
**File:** `resources/reports.md`

| Element | Description |
|---|---|
| Report header | Title + auto-generated date badge |
| Incident header | Title + severity/status meta pills |
| SVG bar chart | Pure SVG bar chart with grid lines and labels |
| Action items checklist | Styled checklist with avatars and strikethrough |
| Carryover panel | Status tags on neutral background |
| Highlights list | Bullet list with accent square markers |

## Usage Guidelines

1. **Don't overdo it.** One or two rich elements per post is usually enough. Let markdown carry the content.
2. **Prefer semantic elements.** Use callouts for genuinely important notes, not for decoration.
3. **Test in both modes.** Preview with light and dark themes before publishing.
4. **Keep JS minimal.** Prefer CSS-only solutions. When JS is needed, keep it vanilla and self-contained.
5. **Accessibility.** Include appropriate `aria-` attributes on interactive elements. Use semantic HTML (`<details>`, `<figure>`, `<table>`) where possible.
