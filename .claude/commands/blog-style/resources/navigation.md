# Navigation & Structure Components

Source material extracted from:
- `07-research-learning.md` (collapsible details, tab interface)
- `02-code-review-understanding.md` (sticky TOC, rollout steps)
- `08-reports.md` (fixed TOC sidebar)

These components handle page structure, wayfinding, and multi-step presentation. All use the electric indigo accent palette in place of the original clay.

---

## Color Tokens (Navigation Components)

All four components use this shared token set. Replace `--clay: #D97757` everywhere it appears in source material with `--accent: #5B5FC7`.

```css
:root {
  /* Accent (electric indigo — replaces --clay throughout) */
  --accent:             #5B5FC7;
  --accent-light:       #EEEEFF;
  --accent-dark:        #3D4096;
  --accent-translucent: rgba(91, 95, 199, 0.10);

  /* Neutrals (unchanged from base design system) */
  --ivory:    #FAF9F5;
  --slate:    #141413;
  --oat:      #E3DACC;
  --gray-150: #F0EEE6;
  --gray-300: #D1CFC5;
  --gray-500: #87867F;
  --gray-700: #3D3D3A;

  /* Typography */
  --serif: ui-serif, Georgia, "Times New Roman", serif;
  --sans:  system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  --mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;
}
```

**Dark mode notes (all components):** On dark backgrounds, swap `--accent-light` to `rgba(91,95,199,0.18)` for tinted surfaces, and use `--accent: #7B80E8` (lightened ~15%) for text/border uses so contrast stays above 4.5:1 against a dark card background.

---

## 1. Sticky Sidebar TOC

**Name:** Sticky Sidebar Table of Contents

**Description:** A fixed-position "On this page" navigation rail with left-border links and scroll-position tracking. The active section link gets an accent-colored left border and slightly darker text. Collapses to hidden below 920px so it does not stack awkwardly on narrow viewports.

**When to use:** Long posts (3+ sections) where readers benefit from knowing where they are and jumping to specific sections. Works best in a two-column layout with a 200–240px left or right column reserved for the nav. For report-style posts needing a fixed sidebar outside the content column, see the fixed variant at the bottom of this component.

**Dark mode notes:** Set card/nav background to a dark surface (e.g., `#1A1A18`). Active border and hover color: use `#7B80E8` (lightened accent). Gray border links remain readable against the dark background without adjustment.

```html
<style>
  :root {
    --accent:             #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91, 95, 199, 0.10);
    --slate:    #141413;
    --gray-150: #F0EEE6;
    --gray-300: #D1CFC5;
    --gray-500: #87867F;
    --gray-700: #3D3D3A;
    --mono: ui-monospace, "SF Mono", Menlo, Consolas, monospace;
    --sans: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  }

  /* Two-column page layout — nav left, content right */
  .toc-layout {
    max-width: 1100px;
    margin: 0 auto;
    display: grid;
    grid-template-columns: 200px minmax(0, 1fr);
    gap: 48px;
  }
  @media (max-width: 920px) {
    .toc-layout { grid-template-columns: 1fr; }
    .sidenav    { display: none; }
  }

  /* Nav rail */
  .sidenav {
    position: sticky;
    top: 32px;
    align-self: start;
    font-family: var(--sans);
    font-size: 13px;
  }

  .sidenav .label {
    font-family: var(--mono);
    font-size: 10px;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--gray-500);
    margin-bottom: 12px;
  }

  /* Base link */
  .sidenav a {
    display: block;
    padding: 5px 0 5px 12px;
    border-left: 2px solid var(--gray-300);
    color: var(--gray-700);
    text-decoration: none;
    transition: border-color 80ms, color 80ms;
  }
  .sidenav a:hover {
    color: var(--slate);
    border-left-color: var(--slate);
  }

  /* Active state (set via JS scroll tracking) */
  .sidenav a.active {
    color: var(--accent-dark);
    border-left-color: var(--accent);
    background: var(--accent-light);
    margin-left: -1px;    /* compensate for 2→3px border shift */
    border-left-width: 3px;
  }

  /* Secondary / indented links */
  .sidenav a.l2 {
    padding-left: 24px;
    font-size: 12.5px;
    color: var(--gray-500);
  }
  .sidenav a.l2.active {
    padding-left: 23px; /* 24 - 1 border compensation */
  }

  /* Optional "Files read" footer */
  .sidenav .files {
    margin-top: 28px;
    border-top: 1px solid var(--gray-300);
    padding-top: 16px;
  }
  .sidenav .files code {
    display: block;
    font-family: var(--mono);
    font-size: 11px;
    color: var(--gray-500);
    padding: 3px 0;
  }

  /* ── Fixed-position variant for report layouts ────────────────────── */
  /* Hides below 1100px where there is no room outside the content col. */
  .toc-fixed { display: none; }
  @media (min-width: 1100px) {
    .toc-fixed {
      display: block;
      position: fixed;
      top: 72px;
      /* Positions just outside a centered 820px content column */
      right: max(24px, calc(50vw - 410px - 190px));
      width: 170px;
      font-family: var(--sans);
      font-size: 13px;
    }
    .toc-fixed .label {
      font-family: var(--mono);
      font-size: 10px;
      text-transform: uppercase;
      letter-spacing: 0.1em;
      color: var(--gray-500);
      margin-bottom: 12px;
    }
    .toc-fixed a {
      display: block;
      color: var(--gray-500);
      text-decoration: none;
      padding: 5px 0 5px 12px;
      border-left: 1.5px solid var(--gray-300);
      line-height: 1.3;
      transition: color 80ms, border-color 80ms;
    }
    .toc-fixed a:hover,
    .toc-fixed a.active {
      color: var(--accent-dark);
      border-left-color: var(--accent);
    }
  }

  /* Smooth scrolling and scroll margin for targets */
  html { scroll-behavior: smooth; }
  .toc-section { scroll-margin-top: 24px; }
</style>

<!-- Sticky sidebar variant (left column of .toc-layout) -->
<nav class="sidenav">
  <div class="label">On this page</div>
  <a href="#overview" class="active">Overview</a>
  <a href="#setup">Setup</a>
  <a href="#step-1" class="l2">1. Install</a>
  <a href="#step-2" class="l2">2. Configure</a>
  <a href="#step-3" class="l2">3. Deploy</a>
  <a href="#results">Results</a>
  <a href="#faq">FAQ</a>
  <div class="files">
    <div class="label">Files referenced</div>
    <code>config/settings.yaml</code>
    <code>src/main.ts</code>
  </div>
</nav>

<!-- Fixed-position TOC variant (for report-style single-column posts) -->
<nav class="toc-fixed">
  <div class="label">On this page</div>
  <a href="#overview">Overview</a>
  <a href="#setup">Setup</a>
  <a href="#results">Results</a>
  <a href="#faq">FAQ</a>
</nav>

<script>
// Scroll-position tracking — highlights the nav link for the section in view.
// Requires section elements to have id attributes matching the nav hrefs.
(function () {
  const links = document.querySelectorAll('.sidenav a[href^="#"], .toc-fixed a[href^="#"]');
  if (!links.length) return;

  const sectionIds = Array.from(links).map(a => a.getAttribute('href').slice(1));
  const sections   = sectionIds
    .map(id => document.getElementById(id))
    .filter(Boolean);

  function onScroll() {
    // Find the last section whose top is above the middle of the viewport.
    const mid = window.scrollY + window.innerHeight / 3;
    let active = sections[0];
    for (const s of sections) {
      if (s.offsetTop <= mid) active = s;
    }
    links.forEach(a => {
      a.classList.toggle('active', a.getAttribute('href') === '#' + active.id);
    });
  }

  window.addEventListener('scroll', onScroll, { passive: true });
  onScroll(); // run once on load
})();
</script>
```

---

## 2. Collapsible Details / Summary

**Name:** Collapsible Details Accordion

**Description:** Native HTML5 `<details>`/`<summary>` accordion with a rotating accent-colored triangle indicator. On open the triangle rotates 90°. An optional right-aligned monospace badge (`.where`) displays a file path or reference. The first item can start open via the `open` attribute. Multiple items stack with consistent 14px vertical margins.

**When to use:** Step-by-step walkthroughs where each step has prose or code the reader may not need immediately. Also suitable for FAQ sections, long configuration explanations, or any content where progressive disclosure reduces cognitive load. The optional `.where` badge is useful for code-tour posts where each step references a specific file and line range.

**Dark mode notes:** Set `details` background to a dark card color (e.g., `#1E1E1C`). Border color: use `rgba(255,255,255,0.10)`. Triangle indicator: keep as `--accent` (or `#7B80E8` if on a very dark surface). The `.where` badge text naturally fades since it uses `--gray-500`.

```html
<style>
  :root {
    --accent:             #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91, 95, 199, 0.10);
    --slate:    #141413;
    --gray-300: #D1CFC5;
    --gray-500: #87867F;
    --serif: ui-serif, Georgia, "Times New Roman", serif;
    --sans:  system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
    --mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }

  /* Container — stack multiple details with consistent spacing */
  .step-group details {
    border: 1.5px solid var(--gray-300);
    border-radius: 10px;
    background: #fff;
    margin: 14px 0;
    overflow: hidden;
  }

  /* Summary row */
  .step-group summary {
    list-style: none;
    cursor: pointer;
    padding: 14px 16px;
    font-family: var(--serif);
    font-size: 16px;
    color: var(--slate);
    display: flex;
    align-items: baseline;
    gap: 10px;
  }
  .step-group summary::-webkit-details-marker { display: none; }

  /* Rotating accent triangle */
  .step-group summary::before {
    content: "▸";
    color: var(--accent);
    font-family: var(--sans);
    font-size: 12px;
    transition: transform 120ms ease;
    display: inline-block;
  }
  .step-group details[open] summary::before {
    transform: rotate(90deg);
  }

  /* Open state: accent left border on the whole card */
  .step-group details[open] {
    border-left-color: var(--accent);
    border-left-width: 3px;
  }

  /* Optional right-aligned file reference badge */
  .step-group summary .where {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--gray-500);
    margin-left: auto;
    background: var(--accent-light);
    padding: 2px 7px;
    border-radius: 5px;
    letter-spacing: 0.01em;
  }

  /* Body content */
  .step-group details .body {
    padding: 0 16px 16px;
  }
  .step-group details .body p {
    font-family: var(--sans);
    font-size: 14px;
    line-height: 1.65;
    margin-bottom: 10px;
    max-width: 640px;
    color: #3D3D3A;
  }
  .step-group details .body p:last-child { margin-bottom: 0; }
  .step-group details .body code {
    font-family: var(--mono);
    font-size: 13px;
  }
</style>

<div class="step-group">
  <details open>
    <summary>
      1 · Identify the caller
      <span class="where">middleware/ratelimit.ts:21</span>
    </summary>
    <div class="body">
      <p>The middleware reduces the request to a <code>bucketKey</code>: API key
      if an <code>Authorization</code> header is present, otherwise the client
      IP. Anonymous IP traffic gets a lower default tier.</p>
    </div>
  </details>

  <details>
    <summary>
      2 · Look up the bucket
      <span class="where">lib/tokenBucket.ts:9</span>
    </summary>
    <div class="body">
      <p>The route name plus bucket key map to a Redis hash holding
      <code>tokens</code> and <code>updatedAt</code>. If the key is missing
      it is created lazily at full capacity.</p>
    </div>
  </details>

  <details>
    <summary>
      3 · Refill and consume
      <span class="where">lib/tokenBucket.ts:31</span>
    </summary>
    <div class="body">
      <p>Refill is computed from elapsed time (<code>rate × Δt</code>, capped
      at <code>burst</code>), then one token is subtracted. The whole
      read-modify-write runs as a single Lua script.</p>
    </div>
  </details>

  <details>
    <summary>
      4 · Reject when empty
      <span class="where">middleware/ratelimit.ts:48</span>
    </summary>
    <div class="body">
      <p>If the script returns <code>tokens &lt; 0</code> the middleware
      short-circuits with <code>429 Too Many Requests</code> and sets
      <code>Retry-After</code>.</p>
    </div>
  </details>
</div>
```

---

## 3. Tab Interface

**Name:** Tabbed Content Panel

**Description:** A horizontal tab bar with a monospace font, gray background, and an accent-colored bottom underline on the active tab. JavaScript toggles the `.on` class to swap visible content panels. Each `<button>` carries a `data-t` index that maps to a `<pre>` pane (for code) or a `<div>` pane (for prose). Both pane types are supported — swap `pre` for `div.tab-pane` when showing non-code content.

**When to use:** Showing related but distinct code samples side by side (e.g., config file, usage snippet, HTTP response). Also works for comparing the same concept across languages, or grouping before/after views. Keep tab count to 4 or fewer — more than that wraps awkwardly on small viewports.

**Dark mode notes:** Tab bar background: use a slightly lighter dark surface (e.g., `#242421`). Active tab background: the main card dark color (e.g., `#1E1E1C`). Active underline stays as `--accent`. Inactive tab text: `--gray-500` works on dark too.

```html
<style>
  :root {
    --accent:             #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91, 95, 199, 0.10);
    --slate:    #141413;
    --gray-150: #F0EEE6;
    --gray-300: #D1CFC5;
    --gray-500: #87867F;
    --mono: ui-monospace, "SF Mono", Menlo, Consolas, monospace;
    --sans: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }

  /* Outer card */
  .tabs {
    border: 1.5px solid var(--gray-300);
    border-radius: 10px;
    background: #fff;
    margin: 16px 0 8px;
    overflow: hidden;
  }

  /* Tab bar */
  .tabbar {
    display: flex;
    border-bottom: 1px solid var(--gray-300);
    background: var(--gray-150);
    overflow-x: auto;          /* allow horizontal scroll on very small viewports */
    scrollbar-width: none;
  }
  .tabbar::-webkit-scrollbar { display: none; }

  /* Tab buttons */
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
    white-space: nowrap;
    transition: color 80ms;
  }
  .tabbar button:last-child { border-right: none; }
  .tabbar button:hover:not(.on) {
    color: var(--slate);
  }

  /* Active tab */
  .tabbar button.on {
    background: #fff;
    color: var(--accent-dark);
    border-bottom: 2px solid var(--accent);
    margin-bottom: -1px;       /* overlap the tab bar bottom border */
  }

  /* Code panes (use <pre> for code content) */
  .tabs pre {
    display: none;
    margin: 0;
    padding: 16px 18px;
    font-family: var(--mono);
    font-size: 12.5px;
    line-height: 1.6;
    color: var(--slate);
    overflow-x: auto;
  }
  .tabs pre.on { display: block; }

  /* Prose panes (use .tab-pane for non-code content) */
  .tab-pane {
    display: none;
    padding: 16px 18px;
    font-family: var(--sans);
    font-size: 14px;
    line-height: 1.65;
    color: #3D3D3A;
  }
  .tab-pane.on { display: block; }

  /* Inline syntax highlighting tokens (for code panes) */
  .hl { color: var(--accent); }           /* highlighted token */
  .cm { color: var(--gray-500); }         /* comment */
</style>

<!-- Code tab example -->
<div class="tabs" data-tabs>
  <div class="tabbar">
    <button class="on" data-t="0">limits.yaml</button>
    <button data-t="1">route.ts</button>
    <button data-t="2">HTTP response</button>
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
document.querySelectorAll("[data-tabs]").forEach(function (box) {
  var btns  = box.querySelectorAll("button");
  var panes = box.querySelectorAll("pre, .tab-pane");
  btns.forEach(function (b) {
    b.addEventListener("click", function () {
      btns.forEach(function (x)  { x.classList.remove("on"); });
      panes.forEach(function (x) { x.classList.remove("on"); });
      b.classList.add("on");
      panes[+b.dataset.t].classList.add("on");
    });
  });
});
</script>
```

---

## 4. Phase / Rollout Steps

**Name:** Horizontal Phase Strip

**Description:** A connected horizontal strip of step cards showing multi-stage progress — deployment phases, migration stages, experiment ramps, or any sequenced plan. Cards share borders at their edges to form a single visual unit. Each card has a monospace phase label (e.g., "Day 0"), a large accent-colored stage identifier (e.g., "10%"), and a small description. Numbered circles can replace the large identifier for ordered steps without percentage values.

**When to use:** Deployment plans, rollout strategies, migration phases, or any post section that describes a sequence of distinct stages with different characteristics. The horizontal layout communicates parallel-track thinking and reads faster than a vertical list for short descriptions. Collapses to a vertical stack on narrow viewports.

**Dark mode notes:** Card background: dark surface (e.g., `#1E1E1C`). Border: `rgba(255,255,255,0.10)`. Stage identifier color: use `#7B80E8` (lightened accent). Phase label and description text: `--gray-500` and `--gray-300` respectively read fine on dark.

```html
<style>
  :root {
    --accent:             #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91, 95, 199, 0.10);
    --slate:    #141413;
    --gray-300: #D1CFC5;
    --gray-500: #87867F;
    --mono: ui-monospace, "SF Mono", Menlo, Consolas, monospace;
    --sans: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }

  /* ── Variant A: labeled phase strip (percentage / stage name) ──── */
  .rollout {
    display: flex;
    max-width: 780px;
  }
  @media (max-width: 680px) {
    .rollout { flex-direction: column; }
  }

  .rollout .step {
    flex: 1;
    background: #fff;
    border: 1.5px solid var(--gray-300);
    padding: 16px 18px;
    position: relative;
  }

  /* Round only the outer corners */
  .rollout .step:first-child { border-radius: 12px 0 0 12px; }
  .rollout .step:last-child  { border-radius: 0 12px 12px 0; }
  .rollout .step + .step     { border-left: none; }

  /* Vertical stack on mobile: restore individual radii and borders */
  @media (max-width: 680px) {
    .rollout .step,
    .rollout .step:first-child,
    .rollout .step:last-child { border-radius: 12px; }
    .rollout .step + .step    {
      border-left: 1.5px solid var(--gray-300);
      margin-top: 10px;
    }
  }

  /* Phase label (e.g., "Day 0", "Phase 1") */
  .rollout .step .when {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--gray-500);
    text-transform: uppercase;
    letter-spacing: 0.05em;
    margin-bottom: 8px;
  }

  /* Large stage identifier */
  .rollout .step .pct {
    font-family: var(--mono);
    font-size: 22px;
    font-weight: 600;
    color: var(--accent);
    margin-bottom: 6px;
  }

  .rollout .step .desc {
    font-family: var(--sans);
    font-size: 13px;
    color: var(--gray-500);
    line-height: 1.5;
  }

  /* ── Variant B: numbered circles with connecting line ─────────── */
  .phase-steps {
    display: flex;
    align-items: flex-start;
    gap: 0;
    max-width: 760px;
    position: relative;
  }
  @media (max-width: 640px) {
    .phase-steps { flex-direction: column; gap: 0; }
  }

  /* Connecting line behind the circles */
  .phase-steps::before {
    content: "";
    position: absolute;
    top: 17px;               /* vertically centered on the 34px circles */
    left: calc(17px + 16px); /* start at center of first circle */
    right: calc(17px + 16px);/* end at center of last circle */
    height: 2px;
    background: var(--gray-300);
    z-index: 0;
  }
  @media (max-width: 640px) {
    .phase-steps::before {
      top: calc(17px + 16px);
      left: 17px;
      right: auto;
      bottom: calc(17px + 16px);
      width: 2px;
      height: auto;
    }
  }

  .phase-step {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 0 16px;
    position: relative;
    z-index: 1;
    text-align: center;
  }
  @media (max-width: 640px) {
    .phase-step {
      flex-direction: row;
      align-items: flex-start;
      text-align: left;
      padding: 0 0 24px 0;
      gap: 14px;
    }
    .phase-step:last-child { padding-bottom: 0; }
  }

  /* Numbered circle */
  .phase-step .circle {
    width: 34px;
    height: 34px;
    border-radius: 50%;
    background: var(--accent-light);
    border: 2px solid var(--accent);
    color: var(--accent-dark);
    font-family: var(--mono);
    font-size: 13px;
    font-weight: 600;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-shrink: 0;
    margin-bottom: 10px;
  }
  @media (max-width: 640px) {
    .phase-step .circle { margin-bottom: 0; margin-top: 2px; }
  }

  /* Completed step: filled circle */
  .phase-step.done .circle {
    background: var(--accent);
    color: #fff;
    border-color: var(--accent-dark);
  }

  .phase-step .step-title {
    font-family: var(--sans);
    font-size: 13px;
    font-weight: 600;
    color: var(--slate);
    margin-bottom: 4px;
  }
  .phase-step .step-desc {
    font-family: var(--sans);
    font-size: 12.5px;
    color: var(--gray-500);
    line-height: 1.45;
  }
</style>

<!-- Variant A: labeled phase strip with percentage identifiers -->
<div class="rollout">
  <div class="step">
    <div class="when">Day 0</div>
    <div class="pct">internal</div>
    <div class="desc">Team only. Watch dead-letter table and worker error rate.</div>
  </div>
  <div class="step">
    <div class="when">Day 2</div>
    <div class="pct">10%</div>
    <div class="desc">Random sample. Alert if error rate exceeds 0.5%.</div>
  </div>
  <div class="step">
    <div class="when">Day 5</div>
    <div class="pct">50%</div>
    <div class="desc">Broader ramp. Monitor p95 latency and queue depth.</div>
  </div>
  <div class="step">
    <div class="when">Day 7</div>
    <div class="pct">100%</div>
    <div class="desc">Full ramp. Remove the old code path in a follow-up.</div>
  </div>
</div>

<!-- Variant B: numbered circles with connecting line -->
<div class="phase-steps" style="margin-top: 32px;">
  <div class="phase-step done">
    <div class="circle">1</div>
    <div>
      <div class="step-title">Schema migration</div>
      <div class="step-desc">Add new columns, backfill defaults.</div>
    </div>
  </div>
  <div class="phase-step done">
    <div class="circle">2</div>
    <div>
      <div class="step-title">Dual-write</div>
      <div class="step-desc">Write to both old and new paths.</div>
    </div>
  </div>
  <div class="phase-step">
    <div class="circle">3</div>
    <div>
      <div class="step-title">Read cutover</div>
      <div class="step-desc">Switch reads to new path; monitor.</div>
    </div>
  </div>
  <div class="phase-step">
    <div class="circle">4</div>
    <div>
      <div class="step-title">Cleanup</div>
      <div class="step-desc">Drop old columns and write path.</div>
    </div>
  </div>
</div>
```

---

## Hugo Integration Notes

These components use raw HTML. In Hugo with PaperMod:

- Paste inside a post using inline HTML directly in markdown (works because `unsafe = true` is set in `hugo.toml`).
- Or wrap in `{{< rawhtml >}}...{{< /rawhtml >}}` for explicit opt-in.
- The sticky sidebar TOC and two-column layout require the surrounding `.toc-layout` grid wrapper. If the blog theme already provides a sidebar, use only the inner `<nav class="sidenav">` and position it yourself.
- The fixed-position TOC variant (`toc-fixed`) is best for standalone report posts. It will overlap other fixed elements (theme header bars) if `top` is not adjusted to clear them.
- JavaScript for the tab switcher and scroll tracker can be placed inline after the component or in `static/js/` and included via a `<script src="/js/tabs.js">` tag.
- All accent color references use `--accent` and derived tokens — swap the `:root` values once to rebrand any component.
