# Text Elements — HTML/CSS Reference

Five inline text elements for blog posts. All use the electric indigo accent palette. Each block is self-contained and copy-paste-ready for Hugo raw HTML passthrough.

---

## Shared Design Tokens

Include this `:root` block in any component's `<style>` tag. The accent variants replace the original clay/terracotta in all five elements below.

```css
:root {
  /* Accent (electric indigo) */
  --accent:             #5B5FC7;
  --accent-light:       #EEEEFF;
  --accent-dark:        #3D4096;
  --accent-translucent: rgba(91,95,199,0.10);

  /* Neutrals */
  --ivory:    #FAF9F5;
  --slate:    #141413;
  --oat:      #E3DACC;
  --gray-150: #F0EEE6;
  --gray-300: #D1CFC5;
  --gray-500: #87867F;
  --gray-700: #3D3D3A;
  --white:    #FFFFFF;

  /* Typography */
  --serif: ui-serif, Georgia, 'Times New Roman', serif;
  --sans:  system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
  --mono:  ui-monospace, 'SF Mono', Menlo, Monaco, monospace;
}
```

---

## 1. Eyebrow Label

**Name:** Eyebrow Label

**Description:** A monospace uppercase category tag placed above a heading to orient the reader to the content type — e.g., "Data Engineering · tutorial" or "Trading · research". It uses a muted gray so it recedes behind the headline.

**When to use:** At the top of a post's header block or at the start of a major section to establish context before the title. Also useful inside cards or panels where the element type needs labeling.

```html
<style>
:root {
  --accent:   #5B5FC7;
  --gray-500: #87867F;
  --slate:    #141413;
  --serif: ui-serif, Georgia, 'Times New Roman', serif;
  --sans:  system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
  --mono:  ui-monospace, 'SF Mono', Menlo, Monaco, monospace;
}
* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: var(--sans); background: #FAF9F5; padding: 32px; -webkit-font-smoothing: antialiased; }

.eyebrow {
  font-family: var(--mono);
  font-size: 11px;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: var(--gray-500);
  margin-bottom: 10px;
}
h1 {
  font-family: var(--serif);
  font-weight: 500;
  font-size: 32px;
  color: var(--slate);
  letter-spacing: -0.01em;
}
</style>

<div class="eyebrow">Data Engineering · tutorial</div>
<h1>Building a streaming pipeline with DuckDB</h1>
```

**Dark mode notes:** The `--gray-500` color reads well on both light and dark backgrounds. If placing on a dark card, swap to `#9C9A93` (the dark-stage muted text value from the design system). The eyebrow never uses the accent color — its purpose is to recede, not draw attention.

---

## 2. TL;DR Callout

**Name:** TL;DR Callout

**Description:** A warm oat-tinted block with a subtle oat-matched border. The label uses a warm brown color (#8A7D6B) rather than the accent — TL;DR has its own visual identity separate from info callouts. No accent edges; the tint alone differentiates the block.

**When to use:** Lead-in to long technical posts, or mid-post to surface a key principle. Place between the header and the first section. One per post for summary; also works for "design principle" or "key takeaway" callouts.

**Global class:** `.j4-tldr` (defined in `assets/css/extended/blog-style.css`)

```html
<div class="j4-tldr">
<div class="j4-tldr-label">TL;DR</div>
<p><code>claude-context-pct</code> returns context window usage as an integer 0-100. Agents check it between tasks to decide whether to keep working or hand off.</p>
</div>
```

The label text can be changed from "TL;DR" to any short label ("Design principle", "Key takeaway", etc.).

**Standalone CSS (if not using global classes):**

```css
.j4-tldr {
    border: 1px solid rgba(227,218,204,0.6);
    border-radius: 8px;
    padding: 16px 20px;
    margin: 24px 0;
    background: rgba(227,218,204,0.25);
}
.j4-tldr-label {
    font-family: ui-monospace, 'SF Mono', Menlo, monospace;
    font-size: 11px;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    color: #8A7D6B;
    margin-bottom: 8px;
    font-weight: 600;
}
.j4-tldr p { margin: 0; font-size: 15px; line-height: 1.55; }
.j4-tldr code {
    font-size: 13px;
    background: rgba(227,218,204,0.4);
    padding: 1px 5px;
    border-radius: 4px;
}
/* Dark mode */
.dark .j4-tldr { border-color: rgba(227,218,204,0.1); background: rgba(227,218,204,0.06); }
.dark .j4-tldr-label { color: #B0A68F; }
.dark .j4-tldr code { background: rgba(227,218,204,0.1); }
```

---

## 3. Info / Callout Box

**Name:** Info / Callout Box

**Description:** A lightly tinted box (accent-translucent background, accent border) with a flex layout: a bold accent icon on the left and prose on the right. Used for tips, important notes, and supplementary guidance that sits inline with body text.

**When to use:** After a code block or explanation where a short note would help, or for "For agents" instructions at the top of a post. Multiple per post is fine.

**Global class:** `.j4-callout` (defined in `assets/css/extended/blog-style.css`)

```html
<div class="j4-callout">
<div class="j4-callout-label">For agents</div>
<p>Install from <a href="#">the repo</a>. Run <code>my-command</code> to get started.</p>
</div>
```

**Standalone CSS (if not using global classes):**

```css
.j4-callout {
    border: 1px solid rgba(91,95,199,0.15);
    border-radius: 8px;
    padding: 14px 18px;
    margin: 24px 0;
    background: rgba(91,95,199,0.10);
}
.j4-callout-label {
    font-family: ui-monospace, 'SF Mono', Menlo, monospace;
    font-size: 11px;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    color: #5B5FC7;
    margin-bottom: 6px;
    font-weight: 600;
}
.j4-callout p { margin: 0; font-size: 14px; line-height: 1.55; }
.j4-callout a { color: #5B5FC7; }
.j4-callout code {
    font-size: 12px;
    background: rgba(91,95,199,0.15);
    padding: 1px 5px;
    border-radius: 4px;
}
/* Dark mode */
.dark .j4-callout { border-color: rgba(91,95,199,0.15); background: rgba(91,95,199,0.06); }
.dark .j4-callout code { background: rgba(91,95,199,0.12); }
```

---

## 4. Warning / Gotchas Panel

**Name:** Warning / Gotchas Panel

**Description:** An amber-tinted panel for caveats, limitations, and scope constraints. Uses amber/warm yellow tones — visually distinct from both the oat TL;DR and the indigo callout. No accent edges; the tint and amber label differentiate it.

**When to use:** Limitations sections, known caveats, scope constraints. Use when the reader needs to know about edge cases or restrictions.

**Global class:** `.j4-warning` (defined in `assets/css/extended/blog-style.css`)

```html
<div class="j4-warning">
<div class="j4-warning-label">Scope</div>
<p>This only reports the <strong>main agent's</strong> context window. A subagent calling <code>claude-context-pct</code> will get the parent's value, not its own.</p>
</div>
```

**Standalone CSS (if not using global classes):**

```css
.j4-warning {
    border: 1px solid rgba(199,142,63,0.2);
    border-radius: 8px;
    padding: 14px 18px;
    margin: 24px 0;
    background: rgba(199,142,63,0.08);
}
.j4-warning-label {
    font-family: ui-monospace, 'SF Mono', Menlo, monospace;
    font-size: 11px;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    color: #C78E3F;
    margin-bottom: 6px;
    font-weight: 600;
}
.j4-warning p, .j4-warning li { margin: 0; font-size: 14px; line-height: 1.55; }
.j4-warning ul { margin: 8px 0 0 0; padding-left: 18px; }
/* Dark mode */
.dark .j4-warning { border-color: rgba(199,142,63,0.15); background: rgba(199,142,63,0.05); }
```

---

## 5. File Tree

**Name:** File Tree

**Description:** A monospace directory listing styled with the onedark code block background (#282c34). Uses syntax-colored spans for directories (blue), tree branches (dim gray), and comments (muted). Consistent with the site's code block aesthetic in both light and dark mode.

**When to use:** Showing project structure, config file layouts, or any directory hierarchy.

**Global class:** `.j4-filetree` (defined in `assets/css/extended/blog-style.css`)

```html
<div class="j4-filetree">
<span class="ft-dir">notes/scheduled/</span><br>
<span class="ft-branch">├──</span> 2026-04-26.md &nbsp;&nbsp;&nbsp; <span class="ft-comment"># surfaces today or later</span><br>
<span class="ft-branch">├──</span> 2026-05-01.md &nbsp;&nbsp;&nbsp; <span class="ft-comment"># surfaces starting May 1</span><br>
<span class="ft-branch">└──</span> README.md
</div>
```

Span classes: `.ft-dir` (blue #61afef), `.ft-branch` (dim #55595f), `.ft-comment` (muted #7f848e). No dark mode overrides needed — the dark background works in both themes.

---

## 6. Prompt / Context Box

**Name:** Prompt / Context Box

**Description:** A neutral gray background box with a subtle border and a small monospace uppercase label. Does not use the accent color — its purpose is to ground and contextualize, not alert.

**When to use:** Before a step-by-step section, or at the start of a code walkthrough where the reader needs framing.

```html
<div style="background: #F0EEE6; border: 1px solid #D1CFC5; border-radius: 8px; padding: 14px 18px; margin: 24px 0;">
<span style="font-family: ui-monospace, monospace; font-size: 11px; text-transform: uppercase; letter-spacing: 0.06em; color: #87867F; display: block; margin-bottom: 6px;">Context</span>
<span style="font-size: 14px; line-height: 1.55;">The migration drops the synchronous write path entirely. All callers now go through the async queue.</span>
</div>
```

---

## Design Approach Summary (Approach B)

All highlighted blocks follow the same structural pattern:
- **Subtle color-matched border** (1px, low-opacity rgba)
- **Light background fill** (tint at 7-25% opacity)
- **No accent edges** — no colored top/left/bottom borders
- **Mono uppercase label** carries the semantic color

| Block Type | Tint Color | Label Color | Global Class |
|---|---|---|---|
| TL;DR | warm oat `rgba(227,218,204,...)` | `#8A7D6B` | `.j4-tldr` |
| Info callout | indigo `rgba(91,95,199,...)` | `#5B5FC7` | `.j4-callout` |
| Warning | amber `rgba(199,142,63,...)` | `#C78E3F` | `.j4-warning` |
| File tree | onedark `#282c34` | n/a | `.j4-filetree` |
| Prompt/context | gray `#F0EEE6` | `#87867F` | (inline) |
