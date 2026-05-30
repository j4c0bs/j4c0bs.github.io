# Layout Components

---

## 1. Two-Column with Left Nav

Sticky sidebar navigation (200px) paired with a main content area. The nav stays fixed while the reader scrolls. Collapses to single column below 920px — nav is hidden on mobile.

**When to use:** Long posts with multiple named sections — feature walkthroughs, multi-step tutorials, reference posts. Lets readers jump directly to any section without scrolling back.

```html
<style>
  :root {
    --ivory:    #FAF9F5;
    --slate:    #141413;
    --accent:   #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91,95,199,0.10);
    --oat:      #E3DACC;
    --gray-150: #F0EEE6;
    --gray-300: #D1CFC5;
    --gray-500: #87867F;
    --gray-700: #3D3D3A;
    --serif: ui-serif, Georgia, "Times New Roman", serif;
    --sans:  system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
    --mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    background: var(--ivory);
    color: var(--gray-700);
    font-family: var(--sans);
    font-size: 15px;
    line-height: 1.65;
    padding: 56px 24px 120px;
    -webkit-font-smoothing: antialiased;
  }

  /* ── Layout grid ─────────────────────────── */
  .page {
    max-width: 1100px;
    margin: 0 auto;
    display: grid;
    grid-template-columns: 200px minmax(0, 1fr);
    gap: 48px;
  }
  @media (max-width: 920px) {
    .page { grid-template-columns: 1fr; }
    .sidenav { display: none; }
  }

  /* ── Sticky nav ──────────────────────────── */
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
  .sidenav a {
    display: block;
    padding: 5px 0 5px 12px;
    border-left: 2px solid var(--gray-300);
    color: var(--gray-700);
    text-decoration: none;
  }
  .sidenav a:hover,
  .sidenav a.active {
    color: var(--accent);
    border-color: var(--accent);
  }
  .sidenav a.l2 {
    padding-left: 24px;
    font-size: 12.5px;
    color: var(--gray-500);
  }
  .sidenav .nav-section {
    margin-top: 28px;
    border-top: 1px solid var(--gray-300);
    padding-top: 16px;
  }

  /* ── Main content ────────────────────────── */
  h1 {
    font-family: var(--serif);
    font-weight: 500;
    font-size: 32px;
    color: var(--slate);
    letter-spacing: -0.01em;
    margin-bottom: 14px;
  }
  h2 {
    font-family: var(--serif);
    font-weight: 500;
    font-size: 22px;
    color: var(--slate);
    margin: 40px 0 14px;
    scroll-margin-top: 24px;
  }
  p { margin-bottom: 12px; max-width: 680px; }
  code { font-family: var(--mono); font-size: 13px; }

  /* ── Dark mode ───────────────────────────── */
  body.dark {
    background: #1a1a1a;
    color: #c8c6be;
  }
  body.dark .sidenav a { color: #a0a09a; border-color: #3a3a38; }
  body.dark .sidenav a:hover,
  body.dark .sidenav a.active { color: #8b8ede; border-color: #8b8ede; }
  body.dark h1, body.dark h2 { color: #f0ede6; }
</style>

<div class="page">

  <nav class="sidenav">
    <div class="label">On this page</div>
    <a href="#overview">Overview</a>
    <a href="#setup">Setup</a>
    <a href="#step-1" class="l2">1. Install</a>
    <a href="#step-2" class="l2">2. Configure</a>
    <a href="#step-3" class="l2">3. Run</a>
    <a href="#gotchas">Gotchas</a>
    <a href="#faq">FAQ</a>
    <div class="nav-section">
      <div class="label">In this repo</div>
      <a href="#src" class="l2" style="font-family:var(--mono);font-size:11px;">src/main.ts</a>
      <a href="#config" class="l2" style="font-family:var(--mono);font-size:11px;">config/app.yaml</a>
    </div>
  </nav>

  <main>
    <h1 id="overview">Post title here</h1>
    <p>Lead paragraph. Replace with your content.</p>

    <h2 id="setup">Setup</h2>
    <p>Section content goes here.</p>

    <h2 id="step-1">1. Install</h2>
    <p>Step content.</p>

    <h2 id="step-2">2. Configure</h2>
    <p>Step content.</p>

    <h2 id="step-3">3. Run</h2>
    <p>Step content.</p>

    <h2 id="gotchas">Gotchas</h2>
    <p>Caveats and sharp edges.</p>

    <h2 id="faq">FAQ</h2>
    <p>Common questions.</p>
  </main>

</div>
```

**Dark mode notes:** PaperMod adds `.dark` to `<body>`. The nav link active/hover state uses `#8b8ede` (a lightened indigo) so it stays readable on dark backgrounds. Card and border colors need to shift to dark-gray equivalents — adjust `--gray-300` and background to taste.

---

## 2. Two-Column with Right Aside

Main content column with a sticky glossary or reference card on the right (240px). The aside stays anchored while the reader scrolls through the article. Collapses to single column below 920px; aside moves below the main content on mobile.

**When to use:** Concept explainer posts, posts that introduce several terms, or any long post where a persistent reference panel (glossary, key facts, related links) adds value without interrupting the prose.

```html
<style>
  :root {
    --ivory:    #FAF9F5;
    --slate:    #141413;
    --accent:   #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91,95,199,0.10);
    --oat:      #E3DACC;
    --gray-150: #F0EEE6;
    --gray-300: #D1CFC5;
    --gray-500: #87867F;
    --gray-700: #3D3D3A;
    --serif: ui-serif, Georgia, "Times New Roman", serif;
    --sans:  system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
    --mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    background: var(--ivory);
    color: var(--gray-700);
    font-family: var(--sans);
    font-size: 15px;
    line-height: 1.65;
    padding: 56px 24px 120px;
    -webkit-font-smoothing: antialiased;
  }

  /* ── Layout grid ─────────────────────────── */
  .page {
    max-width: 1100px;
    margin: 0 auto;
    display: grid;
    grid-template-columns: minmax(0, 1fr) 240px;
    gap: 48px;
  }
  @media (max-width: 920px) {
    .page  { grid-template-columns: 1fr; }
    aside  { order: 2; position: static; }
  }

  /* ── Main content ────────────────────────── */
  h1 {
    font-family: var(--serif);
    font-weight: 500;
    font-size: 33px;
    color: var(--slate);
    letter-spacing: -0.01em;
    margin-bottom: 12px;
  }
  h2 {
    font-family: var(--serif);
    font-weight: 500;
    font-size: 22px;
    color: var(--slate);
    margin: 40px 0 12px;
  }
  p { margin-bottom: 12px; max-width: 680px; }
  code { font-family: var(--mono); font-size: 13px; }

  /* Inline term: dotted underline, links to glossary entry on hover */
  .term {
    border-bottom: 1.5px dotted var(--accent);
    cursor: help;
    color: var(--slate);
  }

  /* ── Glossary aside ──────────────────────── */
  .glossary-aside {
    position: sticky;
    top: 32px;
    align-self: start;
    border: 1.5px solid var(--gray-300);
    border-radius: 12px;
    background: #fff;
    padding: 18px 18px 8px;
    font-family: var(--sans);
  }
  .glossary-aside .label {
    font-family: var(--mono);
    font-size: 10px;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--gray-500);
    margin-bottom: 12px;
  }
  .glossary-aside dl dt {
    font-family: var(--serif);
    font-size: 15px;
    color: var(--slate);
  }
  .glossary-aside dl dd {
    font-size: 12.5px;
    line-height: 1.5;
    color: var(--gray-700);
    margin: 2px 0 14px;
  }
  /* Highlight state when a .term in prose is hovered */
  .glossary-aside dl dt.hl,
  .glossary-aside dl dt.hl + dd {
    background: var(--accent-translucent);
    margin-left: -8px;
    margin-right: -8px;
    padding-left: 8px;
    padding-right: 8px;
    border-radius: 4px;
  }

  /* ── Dark mode ───────────────────────────── */
  body.dark {
    background: #1a1a1a;
    color: #c8c6be;
  }
  body.dark h1, body.dark h2 { color: #f0ede6; }
  body.dark .term { color: #f0ede6; border-color: #8b8ede; }
  body.dark .glossary-aside {
    background: #242424;
    border-color: #3a3a38;
  }
  body.dark .glossary-aside dl dt { color: #f0ede6; }
  body.dark .glossary-aside dl dd { color: #a0a09a; }
  body.dark .glossary-aside dl dt.hl,
  body.dark .glossary-aside dl dt.hl + dd {
    background: rgba(91,95,199,0.18);
  }
</style>

<div class="page">

  <main>
    <h1>Concept title here</h1>
    <p>
      Framing paragraph. Use <span class="term" data-term="key-term">key terms</span>
      inline — hovering them highlights the matching glossary entry in the aside.
      Introduce <span class="term" data-term="second-term">another term</span> naturally.
    </p>

    <h2>The core idea</h2>
    <p>Explanation using <span class="term" data-term="key-term">the term</span> in context.</p>

    <h2>Where you'll see it</h2>
    <p>Real-world usage context.</p>
  </main>

  <aside class="glossary-aside">
    <div class="label">Glossary</div>
    <dl id="gloss">
      <dt data-g="key-term">Key term</dt>
      <dd>Definition of the key term. Keep it to 1–2 sentences.</dd>
      <dt data-g="second-term">Second term</dt>
      <dd>Definition of the second term.</dd>
    </dl>
  </aside>

</div>

<script>
/* Hover a .term in prose → highlight matching dt in the aside */
document.querySelectorAll(".term").forEach(el => {
  const g = el.dataset.term;
  el.addEventListener("mouseenter", () =>
    document.querySelector(`dt[data-g="${g}"]`)?.classList.add("hl")
  );
  el.addEventListener("mouseleave", () =>
    document.querySelector(`dt[data-g="${g}"]`)?.classList.remove("hl")
  );
});
</script>
```

**Dark mode notes:** The aside card needs an explicit dark background (`#242424` works well against PaperMod's dark body). The glossary highlight uses `rgba(91,95,199,0.18)` — slightly stronger than the light-mode translucent to stay visible on dark backgrounds. The `.term` dotted underline switches to `#8b8ede` (lighter indigo) so it reads against dark body text.

---

## 3. Before/After Comparison Grid

Side-by-side cards comparing two states, approaches, or code paths. The "before" panel is neutral; the "after" panel gets a green (olive) border accent to signal the preferred state. Stacks to single column below 680px.

**When to use:** Refactoring posts, migration writeups, design decision posts, any post where showing the contrast between two approaches is the core argument. Works for bullet-point summaries, prose descriptions, or code snippets inside each panel.

```html
<style>
  :root {
    --slate:    #141413;
    --accent:   #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91,95,199,0.10);
    --olive:    #788C5D;
    --oat:      #E3DACC;
    --gray-300: #D1CFC5;
    --gray-500: #87867F;
    --gray-700: #3D3D3A;
    --mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;
    --sans:  system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }

  /* ── Grid ────────────────────────────────── */
  .ba {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 24px;
    margin: 22px 0;
    max-width: 860px;
  }
  @media (max-width: 680px) { .ba { grid-template-columns: 1fr; } }

  /* ── Panels ──────────────────────────────── */
  .ba .panel {
    background: #fff;
    border: 1.5px solid var(--gray-300);
    border-radius: 12px;
    padding: 18px 20px;
  }

  /* "After" panel: olive border signals the preferred state */
  .ba .panel.after { border-color: var(--olive); }

  /* Optional: "after" variant using accent color instead of olive */
  .ba .panel.after-accent { border-color: var(--accent); }

  /* ── Panel header label ──────────────────── */
  .ba .k {
    font-family: var(--mono);
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 0.06em;
    color: var(--gray-500);
    margin-bottom: 10px;
  }
  .ba .after .k        { color: var(--olive); }
  .ba .after-accent .k { color: var(--accent); }

  /* ── Bullet list variant ─────────────────── */
  .ba ul {
    list-style: none;
    padding: 0;
    display: flex;
    flex-direction: column;
    gap: 8px;
    font-family: var(--sans);
    font-size: 13.5px;
    color: var(--gray-700);
  }
  .ba li::before        { content: "·"; color: var(--gray-500); margin-right: 8px; }
  .ba .after li::before { content: "·"; color: var(--olive); margin-right: 8px; }
  .ba .after-accent li::before { content: "·"; color: var(--accent); margin-right: 8px; }
  .ba strong { color: var(--slate); }

  /* ── Prose variant ───────────────────────── */
  .ba p {
    font-family: var(--sans);
    font-size: 13.5px;
    line-height: 1.6;
    color: var(--gray-700);
    margin-bottom: 8px;
  }
  .ba p:last-child { margin-bottom: 0; }
  .ba code {
    font-family: var(--mono);
    font-size: 12.5px;
    background: #F0EEE6;
    padding: 1px 5px;
    border-radius: 4px;
  }

  /* ── Dark mode ───────────────────────────── */
  body.dark .ba .panel {
    background: #242424;
    border-color: #3a3a38;
  }
  body.dark .ba .panel.after       { border-color: #5e7a44; }
  body.dark .ba .panel.after-accent { border-color: #8b8ede; }
  body.dark .ba .k                 { color: #6a6a64; }
  body.dark .ba .after .k          { color: #7a9e58; }
  body.dark .ba .after-accent .k   { color: #8b8ede; }
  body.dark .ba p,
  body.dark .ba li                 { color: #a8a6a0; }
  body.dark .ba strong             { color: #e8e5de; }
  body.dark .ba code               { background: #2e2e2e; color: #d4d2cc; }
</style>

<!-- Bullet list variant -->
<div class="ba">
  <div class="panel">
    <div class="k">Before</div>
    <ul>
      <li>Inline synchronous processing</li>
      <li>Timeout causes a <strong>500</strong> for the caller</li>
      <li>No retries — a dropped item is gone</li>
      <li>p99 latency: <strong>1.4 s</strong></li>
    </ul>
  </div>
  <div class="panel after">
    <div class="k">After</div>
    <ul>
      <li>Handler enqueues a job and returns</li>
      <li>Worker retries 3× with exponential backoff</li>
      <li>Dead-letter table for failed items</li>
      <li>p99 latency: <strong>180 ms</strong></li>
    </ul>
  </div>
</div>

<!-- Prose / description variant -->
<div class="ba">
  <div class="panel">
    <div class="k">Approach A</div>
    <p>
      Explanation of the first approach. Describe the mechanism, trade-offs,
      and where it breaks down. Reference <code>relevant.code()</code> if useful.
    </p>
    <p>Second paragraph if needed.</p>
  </div>
  <div class="panel after-accent">
    <div class="k">Approach B</div>
    <p>
      Explanation of the preferred approach. Lead with what changes and why
      it matters. Mention the key constraint it removes.
    </p>
    <p>Second paragraph if needed.</p>
  </div>
</div>
```

**Dark mode notes:** PaperMod's `.dark` body class is targeted above. The "after" olive border lightens to `#5e7a44` on dark backgrounds. The accent variant uses `#8b8ede` — a lightened indigo that stays visible on dark backgrounds without washing out. Inline code uses `#2e2e2e` background to stay distinct from the panel surface.
