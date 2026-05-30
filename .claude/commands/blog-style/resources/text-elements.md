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

**Description:** A prominent white card with a 4px accent left border. The mono uppercase label "TL;DR" sits above the summary text. Use this as the first element after the post title to give skimmers an immediate answer.

**When to use:** Lead-in to long technical posts — architecture write-ups, deep dives, research notes. Place it between the header and the first section heading. One per post.

```html
<style>
:root {
  --accent:   #5B5FC7;
  --slate:    #141413;
  --gray-300: #D1CFC5;
  --gray-500: #87867F;
  --white:    #FFFFFF;
  --sans: system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
  --mono: ui-monospace, 'SF Mono', Menlo, Monaco, monospace;
}
* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: var(--sans); background: #FAF9F5; padding: 32px; -webkit-font-smoothing: antialiased; }

.tldr {
  background: var(--white);
  border: 1.5px solid var(--gray-300);
  border-left: 4px solid var(--accent);
  border-radius: 12px;
  padding: 20px 24px;
  margin-bottom: 32px;
  max-width: 760px;
}
.tldr .k {
  font-family: var(--mono);
  font-size: 11px;
  text-transform: uppercase;
  letter-spacing: 0.06em;
  color: var(--gray-500);
  margin-bottom: 8px;
}
.tldr p {
  font-family: var(--sans);
  font-size: 15px;
  color: var(--slate);
  line-height: 1.6;
}
</style>

<div class="tldr">
  <div class="k">TL;DR</div>
  <p>Replacing the synchronous notification path with a queue-backed worker
  cut p99 mutation latency from 1.4 s to 180 ms and eliminated silent drops
  when the SMTP pool was exhausted. The old endpoint stays as a no-op for
  one release cycle.</p>
</div>
```

**Dark mode notes:** On dark backgrounds, swap `--white` to `#1F1E1B` and `--gray-300` to `#3D3D3A`. The 4px `--accent` border holds its visual weight on dark surfaces. If the post background is dark, you can use `--accent-translucent` as the card fill instead of white.

---

## 3. Info / Callout Box

**Name:** Info / Callout Box

**Description:** A lightly tinted box (accent-translucent background, accent border) with a flex layout: a bold accent icon on the left and prose on the right. Used for tips, important notes, and supplementary guidance that sits inline with body text.

**When to use:** After a code block or explanation where a short note would help — "if you only need X, skip Y" — or anywhere a tip needs to stand out from the surrounding prose without interrupting the reading flow. Multiple per post is fine.

```html
<style>
:root {
  --accent:             #5B5FC7;
  --accent-light:       #EEEEFF;
  --accent-translucent: rgba(91,95,199,0.10);
  --slate:    #141413;
  --gray-700: #3D3D3A;
  --sans: system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
  --mono: ui-monospace, 'SF Mono', Menlo, Monaco, monospace;
}
* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: var(--sans); background: #FAF9F5; padding: 32px; -webkit-font-smoothing: antialiased; }

.callout {
  display: flex;
  gap: 12px;
  border: 1.5px solid var(--accent);
  background: var(--accent-translucent);
  border-radius: 10px;
  padding: 14px 16px;
  margin: 18px 0;
  font-size: 14px;
  font-family: var(--sans);
  line-height: 1.65;
  max-width: 680px;
}
.callout .ico {
  color: var(--accent);
  font-weight: 700;
  flex-shrink: 0;
  font-size: 15px;
  line-height: 1.4;
}
.callout p { color: var(--gray-700); margin: 0; }
.callout code { font-family: var(--mono); font-size: 13px; }
</style>

<div class="callout">
  <span class="ico">★</span>
  <p>If you only need the default tier, you don't need a YAML entry at all —
  just wrap the handler in <code>rateLimit()</code> with no argument.
  The route name is inferred from the path.</p>
</div>
```

**Dark mode notes:** On dark backgrounds, use `--accent-light: #EEEEFF` at low opacity (e.g. `rgba(238,238,255,0.08)`) for the fill, and keep the `--accent` border. The icon color stays `--accent` — it reads well on dark since `#5B5FC7` is light enough to contrast against near-black. Swap body text to `#E8E6DE` or `var(--ivory)`.

---

## 4. Warning / Gotchas Panel

**Name:** Warning / Gotchas Panel

**Description:** An accent-bordered alert panel for caveats, sharp edges, and common mistakes. The header uses accent color in uppercase mono. List items get small accent square bullet pseudo-elements. Slightly wider than the callout box — designed to hold multi-item warning lists.

**When to use:** End of a section or post where known pitfalls exist. In sidebar layouts, can sit in the right column alongside related key-files lists. Use when the reader needs to know about edge cases before they ship or encounter them in production.

```html
<style>
:root {
  --accent:             #5B5FC7;
  --accent-dark:        #3D4096;
  --accent-translucent: rgba(91,95,199,0.10);
  --gray-500: #87867F;
  --gray-700: #3D3D3A;
  --sans: system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
  --mono: ui-monospace, 'SF Mono', Menlo, Monaco, monospace;
}
* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: var(--sans); background: #FAF9F5; padding: 32px; -webkit-font-smoothing: antialiased; }

.gotchas {
  border: 1.5px solid var(--accent);
  border-radius: 12px;
  background: var(--accent-translucent);
  padding: 18px 20px;
  max-width: 640px;
}
.gotchas h3 {
  font-family: var(--mono);
  font-size: 11px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: var(--accent);
  margin-bottom: 10px;
}
.gotchas ul { list-style: none; padding: 0; }
.gotchas li {
  position: relative;
  padding-left: 16px;
  font-family: var(--sans);
  font-size: 13.5px;
  color: var(--gray-700);
  margin-bottom: 8px;
  line-height: 1.55;
}
.gotchas li:last-child { margin-bottom: 0; }
.gotchas li::before {
  content: "";
  position: absolute;
  left: 0;
  top: 8px;
  width: 5px;
  height: 5px;
  background: var(--accent);
  border-radius: 2px;
}
.gotchas code {
  font-family: var(--mono);
  font-size: 11.5px;
}
</style>

<div class="gotchas">
  <h3>Gotchas</h3>
  <ul>
    <li>The LRU cache is per-process. Revoking a session only clears the
    local cache — other workers may serve it for up to 60 s until their
    TTL lapses.</li>
    <li><code>expiresAt</code> is a <code>timestamptz</code>. The driver
    returns a <code>Date</code> object, so the comparison works, but don't
    refactor to a raw string without adjusting the check.</li>
    <li>Burst limits apply per bucket key, not per IP. A single API key
    can saturate the burst window across multiple client processes.</li>
  </ul>
</div>
```

**Dark mode notes:** On dark backgrounds, bump the border to `--accent` (stays the same) and use a slightly lighter translucent fill — `rgba(91,95,199,0.15)`. The `--accent` header and bullet color remain unchanged. Body text should shift to `#D0CEC5` or similar muted light value. The `--accent-dark: #3D4096` variant is available if you need a darker border on an already-light panel.

---

## 5. Prompt / Context Box

**Name:** Prompt / Context Box

**Description:** A `--gray-150` background box with a subtle border and a small monospace uppercase label (e.g. "Prompt", "Context", "Prerequisites"). Used to display the setup context, the original prompt, or prerequisite knowledge before a technical explanation begins.

**When to use:** Page header area, before a step-by-step section, or at the start of a code walkthrough where the reader needs framing. Does not use the accent color — its purpose is to ground and contextualize, not alert.

```html
<style>
:root {
  --gray-150: #F0EEE6;
  --gray-300: #D1CFC5;
  --gray-500: #87867F;
  --gray-700: #3D3D3A;
  --sans: system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
  --mono: ui-monospace, 'SF Mono', Menlo, Monaco, monospace;
}
* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: var(--sans); background: #FAF9F5; padding: 32px; -webkit-font-smoothing: antialiased; }

.prompt-box {
  background: var(--gray-150);
  border: 1.5px solid var(--gray-300);
  border-radius: 12px;
  padding: 16px 20px;
  font-family: var(--sans);
  font-size: 14.5px;
  color: var(--gray-700);
  max-width: 760px;
}
.prompt-box .label {
  font-family: var(--mono);
  font-size: 11px;
  text-transform: uppercase;
  letter-spacing: 0.06em;
  color: var(--gray-500);
  display: block;
  margin-bottom: 6px;
}
</style>

<div class="prompt-box">
  <span class="label">Context</span>
  The migration drops the synchronous write path entirely. All callers now
  go through the async queue. The old endpoint is kept as a no-op for one
  release cycle to allow gradual traffic migration. Assumes familiarity
  with the existing tRPC router structure.
</div>
```

**Dark mode notes:** This element is intentionally neutral and does not use the accent. On dark backgrounds, swap `--gray-150` to `#1F1E1B` (dark stage panel) and `--gray-300` to `#3D3D3A`. The label color shifts to `#9C9A93` (dark stage muted text). Body text shifts to `#D0CEC5`. This keeps the box visually quieter than the accent-colored callout or TL;DR.

---

## Accent Palette Summary

| Token | Value | Use |
|---|---|---|
| `--accent` | `#5B5FC7` | Primary accent: borders, icons, bullets, labels |
| `--accent-light` | `#EEEEFF` | Light tint for badges or highlights on white |
| `--accent-dark` | `#3D4096` | Darker border on light-filled panels |
| `--accent-translucent` | `rgba(91,95,199,0.10)` | Background fill for callout and gotchas panels |

All five elements source accent colors exclusively from this set. No legacy `--clay` (`#D97757`) references appear in any component above.
