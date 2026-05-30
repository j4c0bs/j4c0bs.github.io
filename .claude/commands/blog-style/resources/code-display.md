# Code Display Elements — Resource File

Source material: `02-code-review-understanding.md`, `07-research-learning.md`

Hugo's fenced code blocks handle the common case well (syntax highlighting via the onedark theme). These elements cover the cases where fenced blocks fall short: diffs with line numbers, files that share context and benefit from tabbed navigation, collapsible snippets that shouldn't dominate the page, and narrative prose with inline change callouts.

All five elements are self-contained — styles are scoped, no external dependencies.

---

## Design Tokens (Code Display Subset)

Paste this `:root` block into any `<style>` that uses these components. The `--accent` family replaces the global `--clay` token for interactive and structural highlights in code display contexts.

```css
:root {
  /* Accent — electric indigo */
  --accent:             #5B5FC7;
  --accent-light:       #EEEEFF;
  --accent-dark:        #3D4096;
  --accent-translucent: rgba(91,95,199,0.10);

  /* Surfaces */
  --slate:    #141413;   /* dark code background, primary text */
  --ivory:    #FAF9F5;   /* page background */
  --white:    #FFFFFF;

  /* Diff colors (semantic — do not replace with accent) */
  --olive:    #788C5D;   /* additions / green */
  --rust:     #B04A3F;   /* deletions / red */

  /* Grays */
  --gray-150: #F0EEE6;   /* subtle bg, tab bar */
  --gray-300: #D1CFC5;   /* borders */
  --gray-500: #87867F;   /* secondary text, line numbers, comments */
  --gray-700: #3D3D3A;   /* body text */

  /* Typography */
  --mono: ui-monospace, 'SF Mono', Menlo, Monaco, monospace;
  --sans: system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
}
```

---

## 1. Diff Block

**Name:** Unified Diff View

**Description:** Dark-background code diff with two columns of line numbers (old + new), a marker column, and code content. Three row variants — `ctx` (context), `add` (addition), `del` (deletion) — plus a `hunk` header row for `@@ ... @@` markers. The grid layout aligns markers and code cleanly without table markup.

**When to use:** Posts about refactoring, migrations, bug fixes, or any before/after code change where showing the diff directly is clearer than describing it in prose. Prefer this over two separate code blocks when the change is small enough to read as a diff.

**Dark mode note:** The component is already dark (slate background). It renders correctly in both light and dark page themes with no modification.

```html
<style>
  :root {
    --accent:             #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91,95,199,0.10);
    --slate:    #141413;
    --olive:    #788C5D;
    --rust:     #B04A3F;
    --gray-500: #87867F;
    --mono:     ui-monospace, 'SF Mono', Menlo, Monaco, monospace;
  }

  .diff {
    background: var(--slate);
    font-family: var(--mono);
    font-size: 12.5px;
    line-height: 1.7;
    overflow-x: auto;
    border-radius: 10px;
  }

  /*
    Four-column grid:
      old line-number | new line-number | marker (+/-/ ) | code
    Adjust the first two column widths if you need wider line numbers.
  */
  .diff-row {
    display: grid;
    grid-template-columns: 40px 40px 18px 1fr;
    align-items: baseline;
    padding: 0 14px 0 0;
    white-space: pre;
  }

  /* Line number columns */
  .diff-row .ln-old,
  .diff-row .ln-new {
    text-align: right;
    padding-right: 12px;
    color: var(--gray-500);
    user-select: none;
  }

  /* +/- marker column */
  .diff-row .mark {
    text-align: center;
    color: var(--gray-500);
  }

  /* Code column defaults */
  .diff-row .code { color: #E8E6DC; }

  /* Context line — dimmer text */
  .diff-row.ctx .code { color: #B8B6AC; }

  /* Addition */
  .diff-row.add { background: rgba(120, 140, 93, 0.15); }
  .diff-row.add .mark { color: var(--olive); }
  .diff-row.add .code { color: #E8E6DC; }

  /* Deletion */
  .diff-row.del { background: rgba(176, 74, 63, 0.15); }
  .diff-row.del .mark { color: var(--rust); }
  .diff-row.del .code { color: #E8E6DC; }

  /* Hunk header (@@ ... @@) */
  .diff-row.hunk { background: rgba(255, 255, 255, 0.04); }
  .diff-row.hunk .code { color: var(--gray-500); }
</style>

<div class="diff">
  <!-- Hunk header: leave line number cells empty -->
  <div class="diff-row hunk">
    <span class="ln-old"></span>
    <span class="ln-new"></span>
    <span class="mark"></span>
    <span class="code">@@ -42,7 +42,6 @@ export function processItems(list) {</span>
  </div>
  <!-- Context line: same number in both columns -->
  <div class="diff-row ctx">
    <span class="ln-old">42</span>
    <span class="ln-new">42</span>
    <span class="mark"> </span>
    <span class="code">  const results = [];</span>
  </div>
  <!-- Deletion: old line number only, new column blank -->
  <div class="diff-row del">
    <span class="ln-old">43</span>
    <span class="ln-new"></span>
    <span class="mark">-</span>
    <span class="code">  for (const item of list) {</span>
  </div>
  <div class="diff-row del">
    <span class="ln-old">44</span>
    <span class="ln-new"></span>
    <span class="mark">-</span>
    <span class="code">    results.push(await fetchItem(item.id));</span>
  </div>
  <div class="diff-row del">
    <span class="ln-old">45</span>
    <span class="ln-new"></span>
    <span class="mark">-</span>
    <span class="code">  }</span>
  </div>
  <!-- Addition: new line number only, old column blank -->
  <div class="diff-row add">
    <span class="ln-old"></span>
    <span class="ln-new">43</span>
    <span class="mark">+</span>
    <span class="code">  const results = await Promise.all(</span>
  </div>
  <div class="diff-row add">
    <span class="ln-old"></span>
    <span class="ln-new">44</span>
    <span class="mark">+</span>
    <span class="code">    list.map(item =&gt; fetchItem(item.id))</span>
  </div>
  <div class="diff-row add">
    <span class="ln-old"></span>
    <span class="ln-new">45</span>
    <span class="mark">+</span>
    <span class="code">  );</span>
  </div>
  <!-- Context line -->
  <div class="diff-row ctx">
    <span class="ln-old">46</span>
    <span class="ln-new">46</span>
    <span class="mark"> </span>
    <span class="code">  return results;</span>
  </div>
</div>
```

---

## 2. Dark Code Panel

**Name:** Dark Code Panel with Token Coloring

**Description:** A dark-background (`#141413`) code block with class-based syntax token coloring. Tokens are marked with inline `<span>` elements carrying semantic class names: `.kw` (keywords), `.str` (strings), `.cm` (comments), `.fn` (function names), `.num` (numbers). The accent color (`--accent`) is used for keywords, replacing the warm-gold tone from the original design.

**When to use:** When Hugo's fenced code block syntax highlighting isn't available or sufficient — for example, inside a `<details>` accordion, as part of a custom layout, or when you need to highlight specific tokens with the blog's accent color rather than the onedark palette. For standalone code snippets, prefer Hugo's fenced blocks. Use this for contextually embedded code.

**Dark mode note:** Self-contained dark surface. No dark-mode media query needed.

```html
<style>
  :root {
    --accent:             #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91,95,199,0.10);
    --slate:    #141413;
    --olive:    #788C5D;
    --gray-500: #87867F;
    --mono:     ui-monospace, 'SF Mono', Menlo, Monaco, monospace;
  }

  .code-panel {
    background: var(--slate);
    border-radius: 10px;
    padding: 16px 18px;
    overflow-x: auto;
  }

  .code-panel pre {
    font-family: var(--mono);
    font-size: 12.5px;
    line-height: 1.65;
    color: #E8E6DE;
    white-space: pre;
    margin: 0;
  }

  /* Token classes */
  .code-panel .kw  { color: var(--accent); }          /* keywords, control flow */
  .code-panel .str { color: var(--olive); }            /* strings */
  .code-panel .cm  { color: var(--gray-500); }         /* comments */
  .code-panel .fn  { color: #C9B98A; }                 /* function / method names */
  .code-panel .num { color: var(--accent-light); }     /* numeric literals */
  .code-panel .dim { color: var(--gray-500); }         /* de-emphasized (path comments, etc.) */
</style>

<div class="code-panel"><pre><span class="dim">// src/queue/worker.ts</span>
<span class="kw">export async function</span> <span class="fn">processJob</span>(job: Job): Promise&lt;<span class="kw">void</span>&gt; {
  <span class="kw">const</span> { name, data } = job;

  <span class="kw">if</span> (job.retryCount &gt; <span class="num">3</span>) {
    <span class="kw">await</span> deadLetter.<span class="fn">insert</span>({ job, reason: <span class="str">'max_retries'</span> });
    <span class="kw">return</span>;
  }

  <span class="kw">const</span> handler = registry.<span class="fn">get</span>(name);
  <span class="kw">if</span> (!handler) <span class="kw">throw new</span> Error(<span class="str">`unknown job: <span class="kw">${</span>name<span class="kw">}</span>`</span>);

  <span class="kw">await</span> <span class="fn">handler</span>(data);  <span class="cm">// throws → queue retries with backoff</span>
}</pre></div>
```

---

## 3. Tabbed Code Viewer

**Name:** Tabbed Code Viewer

**Description:** A multi-tab panel that groups related code files or snippets under a shared tab bar. The active tab gets a bottom-border underline in `--accent`. JavaScript swaps the `.on` class between `<pre>` panes when a tab button is clicked. Supports inline syntax highlighting with `.hl` (accent) and `.cm` (comment gray) spans inside the `<pre>` blocks.

**When to use:** When a post covers multiple related files (config + implementation + test, or before/after versions of the same file) that readers need to compare or refer to together. Keeps the page compact without forcing readers to scroll back and forth between separate code blocks.

**Dark mode note:** The tab bar uses `--gray-150` (light warm gray) and `#fff` for the active panel. On a dark-mode page, these will stand out. If the page is dark, change `.tabbar` background to `#1e1e1c` and `.tabbar button.on` background to `#141413`.

```html
<style>
  :root {
    --accent:             #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91,95,199,0.10);
    --slate:    #141413;
    --gray-150: #F0EEE6;
    --gray-300: #D1CFC5;
    --gray-500: #87867F;
    --mono:     ui-monospace, 'SF Mono', Menlo, Monaco, monospace;
  }

  .tabs {
    border: 1.5px solid var(--gray-300);
    border-radius: 10px;
    background: #fff;
    margin: 16px 0 8px;
    overflow: hidden;
  }

  .tabbar {
    display: flex;
    border-bottom: 1px solid var(--gray-300);
    background: var(--gray-150);
  }

  .tabbar button {
    appearance: none;
    border: none;
    background: none;
    font-family: var(--mono);
    font-size: 12px;
    color: var(--gray-500);
    padding: 10px 16px;
    cursor: pointer;
    border-right: 1px solid var(--gray-300);
    /* Prevent layout shift when border-bottom is applied */
    border-bottom: 2px solid transparent;
    margin-bottom: -1px;
    transition: color 0.1s;
  }

  .tabbar button:hover {
    color: var(--slate);
  }

  /* Active tab: white background + accent underline */
  .tabbar button.on {
    background: #fff;
    color: var(--slate);
    border-bottom: 2px solid var(--accent);
  }

  /* Code panes: hidden by default, shown when .on */
  .tabs pre {
    display: none;
    margin: 0;
    padding: 16px 18px;
    font-family: var(--mono);
    font-size: 12.5px;
    line-height: 1.6;
    color: var(--slate);
    overflow-x: auto;
    white-space: pre;
  }

  .tabs pre.on { display: block; }

  /* Inline token classes */
  .tabs .hl { color: var(--accent); }       /* highlighted tokens */
  .tabs .cm { color: var(--gray-500); }     /* comments */
</style>

<div class="tabs" data-tabs>
  <div class="tabbar">
    <button class="on" data-t="0">limits.yaml</button>
    <button data-t="1">route.ts</button>
    <button data-t="2">response</button>
  </div>
<pre class="on"><span class="cm"># config/limits.yaml</span>
default:
  rate: 100/min
  burst: 120

<span class="hl">search.query</span>:
  rate: 20/min
  burst: 40
  key: api_key        <span class="cm"># or: ip</span></pre>
<pre><span class="cm">// routes/search.ts</span>
router.post(
  "/search",
  <span class="hl">rateLimit("search.query")</span>,
  handler,
);</pre>
<pre>HTTP/1.1 429 Too Many Requests
Retry-After: 17
X-RateLimit-Limit: 20
X-RateLimit-Remaining: 0

{ "error": "rate_limited", "retry_after": 17 }</pre>
</div>

<script>
document.querySelectorAll("[data-tabs]").forEach(function(box) {
  var btns  = box.querySelectorAll("button");
  var panes = box.querySelectorAll("pre");
  btns.forEach(function(b) {
    b.addEventListener("click", function() {
      btns.forEach(function(x)  { x.classList.remove("on"); });
      panes.forEach(function(x) { x.classList.remove("on"); });
      b.classList.add("on");
      panes[+b.dataset.t].classList.add("on");
    });
  });
});
</script>
```

---

## 4. Collapsible Accordion

**Name:** Collapsible File Accordion (Chevron Style)

**Description:** A `<details>`/`<summary>` element styled as a card with a CSS-drawn chevron arrow that rotates 45° when open. The summary bar shows a file path on the left and optional status badge + diff stats on the right. The header background is `--gray-150` (light warm gray) to visually separate it from the body. Body content can be prose, a diff block, or any element.

**When to use:** Long code samples or file-level explanations in posts about architecture walkthroughs, migrations, or code reviews — any context where the content is useful but shouldn't dominate the page by default. Good for secondary files, supporting evidence, or "if you want to go deeper" content.

**Dark mode note:** The card uses a white background with a gray header. On dark pages, swap `background: #fff` to `background: #1e1e1c` and `background: var(--gray-150)` to `background: #252522`.

```html
<style>
  :root {
    --accent:             #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91,95,199,0.10);
    --slate:    #141413;
    --olive:    #788C5D;
    --oat:      #E3DACC;
    --gray-150: #F0EEE6;
    --gray-300: #D1CFC5;
    --gray-500: #87867F;
    --gray-700: #3D3D3A;
    --mono:     ui-monospace, 'SF Mono', Menlo, Monaco, monospace;
    --sans:     system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
  }

  details.file-accordion {
    background: #fff;
    border: 1.5px solid var(--gray-300);
    border-radius: 12px;
    overflow: hidden;
    margin-bottom: 16px;
  }

  details.file-accordion summary {
    padding: 14px 20px;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 12px;
    list-style: none;
    background: var(--gray-150);
    border-bottom: 1.5px solid var(--gray-300);
  }

  /* Remove border-bottom when closed (nothing below the header) */
  details.file-accordion:not([open]) summary {
    border-bottom: none;
  }

  details.file-accordion summary::-webkit-details-marker { display: none; }

  /* CSS chevron: a rotated square with two visible borders */
  .file-accordion .chev {
    width: 8px;
    height: 8px;
    border-right: 2px solid var(--gray-500);
    border-bottom: 2px solid var(--gray-500);
    transform: rotate(-45deg);   /* pointing right = closed */
    transition: transform 0.15s ease;
    flex-shrink: 0;
  }

  /* Rotate to point down when open */
  details.file-accordion[open] .chev {
    transform: rotate(45deg);
  }

  .file-accordion .file-path {
    font-family: var(--mono);
    font-size: 13px;
    color: var(--slate);
    flex: 1;
  }

  /* Status badges: new / mod / del */
  .badge-status {
    font-family: var(--mono);
    font-size: 10.5px;
    text-transform: uppercase;
    letter-spacing: 0.05em;
    padding: 2px 8px;
    border-radius: 6px;
    font-weight: 600;
  }

  .badge-status.new { background: #E4E9DC; color: #4B5C39; }
  .badge-status.mod { background: var(--oat); color: var(--slate); }
  .badge-status.del { background: #F3D9CC; color: #8A3B1E; }

  /* Diff stats */
  .file-stat {
    font-family: var(--mono);
    font-size: 12px;
  }
  .file-stat .add { color: var(--olive); }
  .file-stat .del { color: #B04A3F; }

  /* Body area */
  .file-accordion .accordion-body {
    padding: 18px 20px 22px;
    font-family: var(--sans);
    font-size: 14.5px;
    color: var(--gray-700);
    line-height: 1.6;
  }

  .file-accordion .accordion-body p { max-width: 680px; }
  .file-accordion .accordion-body strong { color: var(--slate); }
</style>

<!-- Open by default with the `open` attribute -->
<details class="file-accordion" open>
  <summary>
    <span class="chev"></span>
    <span class="file-path">packages/notify/src/worker.ts</span>
    <span class="badge-status new">new</span>
    <span class="file-stat"><span class="add">+126</span></span>
  </summary>
  <div class="accordion-body">
    <p><strong>The heart of the change.</strong> A queue subscriber that pulls
    <code style="font-family:monospace">notify.deliver</code> jobs, resolves user
    channel preferences, and calls the right adapter. Retries are configured
    per-channel.</p>
  </div>
</details>

<!-- Closed by default — no `open` attribute -->
<details class="file-accordion">
  <summary>
    <span class="chev"></span>
    <span class="file-path">packages/api/src/routers/comments.ts</span>
    <span class="badge-status mod">mod</span>
    <span class="file-stat"><span class="add">+14</span> <span class="del">−62</span></span>
  </summary>
  <div class="accordion-body">
    <p>Where the performance win shows up. Replaces inline sends with queue
    inserts.</p>
  </div>
</details>
```

---

## 5. Inline Diff

**Name:** Inline Diff (Narrative Style)

**Description:** Embeds deletion and insertion markers directly inside a dark code block, suitable for posts written as a narrative walkthrough of a change. Deletions get `rust` strikethrough text (`#B04A3F`) with a semi-transparent rust background. Insertions get an `olive` background tint (`#788C5D`). Unlike the diff block (element 1), this doesn't use line numbers — it reads as a continuous code listing with visual change callouts. The `.del` and `.add` spans are `display: inline-block; width: 100%` to color the full line width.

**When to use:** When explaining a specific, focused change inline with prose. Works well in posts that walk through a single file change where you want the diff to feel like part of the narrative rather than a separate code review artifact. For larger diffs with multiple hunks, use the diff block (element 1) instead.

**Dark mode note:** Self-contained dark surface. No dark-mode adaptation needed.

```html
<style>
  :root {
    --accent:             #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91,95,199,0.10);
    --slate:    #141413;
    --olive:    #788C5D;
    --rust:     #B04A3F;
    --gray-500: #87867F;
    --mono:     ui-monospace, 'SF Mono', Menlo, Monaco, monospace;
  }

  .inline-diff {
    background: var(--slate);
    border-radius: 10px;
    padding: 16px 18px;
    overflow-x: auto;
  }

  .inline-diff pre {
    font-family: var(--mono);
    font-size: 12.5px;
    line-height: 1.65;
    color: #E8E6DE;
    white-space: pre;
    margin: 0;
  }

  /* Syntax token classes (optional — use as needed) */
  .inline-diff .kw  { color: var(--accent); }
  .inline-diff .str { color: var(--olive); }
  .inline-diff .cm  { color: var(--gray-500); }
  .inline-diff .fn  { color: #C9B98A; }

  /*
    Change lines: display:inline-block + width:100% ensures the
    background color extends to the full line width, not just the text.
  */
  .inline-diff .del {
    display: inline-block;
    width: 100%;
    background: rgba(176, 74, 63, 0.18);      /* rust tint */
    text-decoration: line-through;
    text-decoration-color: rgba(176, 74, 63, 0.6);
  }

  .inline-diff .add {
    display: inline-block;
    width: 100%;
    background: rgba(120, 140, 93, 0.22);     /* olive tint */
  }
</style>

<div class="inline-diff"><pre>  <span class="kw">const</span> data = <span class="kw">await</span> db.records.<span class="fn">find</span>(id);

<span class="del">  <span class="kw">await</span> <span class="fn">sendEmail</span>(data.email, data);   <span class="cm">// blocked the response</span></span>
<span class="del">  <span class="kw">await</span> <span class="fn">sendSlack</span>(data.userId, data);</span>
<span class="add">  <span class="kw">await</span> queue.<span class="fn">insert</span>([</span>
<span class="add">    { name: <span class="str">'notify.email'</span>, data: { userId: data.userId } },</span>
<span class="add">    { name: <span class="str">'notify.slack'</span>, data: { userId: data.userId } },</span>
<span class="add">  ]);</span>
  <span class="kw">return</span> data;</pre></div>
```

---

## Accent Color Quick Reference

| Token | Value | Usage in these components |
|---|---|---|
| `--accent` | `#5B5FC7` | Active tab underline, keyword tokens (`.kw`), accordion chevron hover |
| `--accent-light` | `#EEEEFF` | Numeric literal tokens (`.num`), subtle highlight backgrounds |
| `--accent-dark` | `#3D4096` | Pressed/focus states if needed |
| `--accent-translucent` | `rgba(91,95,199,0.10)` | Hover backgrounds, focus rings |
| `--olive` | `#788C5D` | Additions (diff green), string tokens |
| `--rust` | `#B04A3F` | Deletions (diff red), inline del strikethrough |

## When Not to Use These Elements

- **Single-language snippet with no diffs:** Use Hugo's fenced code block with a language hint — the onedark highlighting is better than hand-rolled token spans.
- **Large multi-file diff (10+ hunks):** Link to the GitHub diff instead. Embedded diffs at that scale hurt readability.
- **Tab viewer with more than 4–5 tabs:** A tabbed viewer with many tabs is harder to scan than separate named sections. Split into subsections instead.
