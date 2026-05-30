# Cards Resource — Blog Style Guide

Card components for the j4c0bs blog. All accent tokens use electric indigo (`--accent: #5B5FC7`) in place of the original clay accent.

---

## Accent Tokens

Add these to any `:root` block alongside the base design tokens whenever using accent-colored card variants.

```css
:root {
  --accent:             #5B5FC7;              /* electric indigo — primary accent */
  --accent-light:       #EEEEFF;              /* tinted surface, badge backgrounds */
  --accent-dark:        #3D4096;              /* hover states, deeper accent */
  --accent-translucent: rgba(91,95,199,0.10); /* focus ring, stripe washes */
}
```

---

## 1. Card Base + 6 Variants

**Name:** Card Base + Variant Matrix

**Description:** A content card with an avatar (circle, 36px, oat bg), serif title, gray subtitle, inline chip tags, and a ghost action button. The base `.card` class handles layout; six modifier classes apply different visual treatments. All cards share `border-radius: 12px`. Hover shows a 2px accent outline.

**When to use:** Pick the variant that matches the surrounding surface and information hierarchy:
- Flat — dense lists on tinted/ivory backgrounds
- Outlined — default content cards on white or ivory
- Elevated — draggable items, modals, popovers
- Accent stripe — pinned or priority items that need to stand out
- Inset — nested cards inside white panels
- Horizontal — compact row lists, sidebars

**Dark mode notes:** Swap `--ivory` → `#1A1A2E`, `--white` → `#12122A`, `--gray-300` → `#2E2E4A`, `--gray-500` → `#8888AA`, `--gray-700` → `#CCCCEE`, `--slate` → `#F0F0FF`. The accent stripe color (`--accent`) and hover outline remain unchanged. For `.v-inset` on dark backgrounds use `background: #1E1E38` and semi-transparent chip fills.

```html
<style>
:root {
  --ivory:              #FAF9F5;
  --slate:              #141413;
  --oat:                #E3DACC;
  --white:              #FFFFFF;
  --gray-100:           #F0EEE6;
  --gray-300:           #D1CFC5;
  --gray-500:           #87867F;
  --gray-700:           #3D3D3A;
  --olive:              #788C5D;
  --accent:             #5B5FC7;
  --accent-light:       #EEEEFF;
  --accent-dark:        #3D4096;
  --accent-translucent: rgba(91,95,199,0.10);
  --serif: ui-serif, Georgia, serif;
  --sans:  system-ui, -apple-system, sans-serif;
  --mono:  ui-monospace, 'SF Mono', Menlo, monospace;
}
* { box-sizing: border-box; }

/* ── Card base ── */
.card {
  border-radius: 12px;
  padding: 20px;
  transition: box-shadow 0.15s ease, outline 0.1s ease;
  cursor: pointer;
}
.card:hover { outline: 2px solid var(--accent); outline-offset: 2px; }

.card-head {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 12px;
}
.avatar {
  width: 36px; height: 36px;
  border-radius: 50%;
  background: var(--oat);
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 13px;
  font-weight: 600;
  color: var(--gray-700);
  font-family: var(--sans);
}
.card-titles { min-width: 0; }
.card-title {
  font-family: var(--serif);
  font-size: 17px;
  font-weight: 500;
  margin: 0 0 2px;
  line-height: 1.3;
  color: var(--slate);
}
.card-sub {
  font-size: 13px;
  color: var(--gray-500);
  margin: 0;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  font-family: var(--sans);
}
.chips {
  display: flex;
  gap: 6px;
  margin-bottom: 14px;
}
.chip {
  display: inline-flex;
  align-items: center;
  height: 20px;
  padding: 0 8px;
  font-size: 11px;
  font-weight: 500;
  border-radius: 999px;
  background: var(--gray-100);
  color: var(--gray-700);
  font-family: var(--mono);
}
.chip.olive { background: rgba(120,140,93,0.16); color: var(--olive); }
.chip.accent { background: var(--accent-light); color: var(--accent-dark); }
.ghost-btn {
  display: inline-flex;
  align-items: center;
  height: 30px;
  padding: 0 12px;
  font-size: 13px;
  font-weight: 500;
  color: var(--gray-700);
  background: transparent;
  border: 1.5px solid var(--gray-300);
  border-radius: 8px;
  cursor: pointer;
  font-family: var(--sans);
}
.ghost-btn:hover { background: var(--gray-100); }

/* ── Variant A: Flat ── */
.v-flat { background: var(--white); border: none; box-shadow: none; }

/* ── Variant B: Outlined ── */
.v-outlined { background: var(--white); border: 1.5px solid var(--gray-300); box-shadow: none; }

/* ── Variant C: Elevated ── */
.v-elevated { background: var(--white); border: none; box-shadow: 0 4px 14px rgba(20,20,19,0.08); }

/* ── Variant D: Accent stripe (4px left border) ── */
.v-stripe {
  background: var(--white);
  border: 1.5px solid var(--gray-300);
  border-left: 4px solid var(--accent);
  box-shadow: none;
}

/* ── Variant E: Inset ── */
.v-inset    { background: var(--oat); border: none; box-shadow: none; }
.v-inset .chip      { background: rgba(255,255,255,0.6); }
.v-inset .ghost-btn { border-color: rgba(20,20,19,0.15); }
.v-inset .avatar    { background: var(--white); }

/* ── Variant F: Horizontal ── */
.v-horizontal {
  background: var(--white);
  border: 1.5px solid var(--gray-300);
  box-shadow: none;
  display: flex;
  align-items: center;
  gap: 14px;
}
.v-horizontal .card-head { margin: 0; flex: 1; min-width: 0; }
.v-horizontal .chips     { margin: 0; }
.v-horizontal .card-sub  { display: none; }
.v-horizontal .ghost-btn { flex-shrink: 0; }

/* ── 3-column responsive layout grid ── */
.card-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 28px;
}
@media (max-width: 960px) { .card-grid { grid-template-columns: repeat(2, 1fr); } }
@media (max-width: 640px) { .card-grid { grid-template-columns: 1fr; } }

.variant-label {
  display: inline-flex;
  align-items: center;
  height: 22px;
  padding: 0 9px;
  font-family: var(--mono);
  font-size: 11px;
  font-weight: 500;
  color: var(--gray-700);
  background: var(--gray-100);
  border-radius: 999px;
  margin-bottom: 10px;
}
.variant-note {
  font-size: 12px;
  color: var(--gray-500);
  margin-top: 10px;
  font-family: var(--sans);
}
</style>

<div class="card-grid">

  <!-- A: Flat -->
  <div>
    <span class="variant-label">A &middot; Flat</span>
    <div class="card v-flat">
      <div class="card-head">
        <div class="avatar">WP</div>
        <div class="card-titles">
          <p class="card-title">Weekly planning</p>
          <p class="card-sub">12 tasks &middot; due Friday</p>
        </div>
      </div>
      <div class="chips">
        <span class="chip">Q2</span>
        <span class="chip olive">Roadmap</span>
      </div>
      <button class="ghost-btn">Open</button>
    </div>
    <p class="variant-note">best for: dense lists on tinted backgrounds</p>
  </div>

  <!-- B: Outlined -->
  <div>
    <span class="variant-label">B &middot; Outlined</span>
    <div class="card v-outlined">
      <div class="card-head">
        <div class="avatar">WP</div>
        <div class="card-titles">
          <p class="card-title">Weekly planning</p>
          <p class="card-sub">12 tasks &middot; due Friday</p>
        </div>
      </div>
      <div class="chips">
        <span class="chip">Q2</span>
        <span class="chip olive">Roadmap</span>
      </div>
      <button class="ghost-btn">Open</button>
    </div>
    <p class="variant-note">best for: default content cards on ivory or white</p>
  </div>

  <!-- C: Elevated -->
  <div>
    <span class="variant-label">C &middot; Elevated</span>
    <div class="card v-elevated">
      <div class="card-head">
        <div class="avatar">WP</div>
        <div class="card-titles">
          <p class="card-title">Weekly planning</p>
          <p class="card-sub">12 tasks &middot; due Friday</p>
        </div>
      </div>
      <div class="chips">
        <span class="chip">Q2</span>
        <span class="chip olive">Roadmap</span>
      </div>
      <button class="ghost-btn">Open</button>
    </div>
    <p class="variant-note">best for: draggable items, popovers</p>
  </div>

  <!-- D: Accent stripe -->
  <div>
    <span class="variant-label">D &middot; Accent stripe</span>
    <div class="card v-stripe">
      <div class="card-head">
        <div class="avatar">WP</div>
        <div class="card-titles">
          <p class="card-title">Weekly planning</p>
          <p class="card-sub">12 tasks &middot; due Friday</p>
        </div>
      </div>
      <div class="chips">
        <span class="chip">Q2</span>
        <span class="chip accent">Pinned</span>
      </div>
      <button class="ghost-btn">Open</button>
    </div>
    <p class="variant-note">best for: pinned or priority items</p>
  </div>

  <!-- E: Inset -->
  <div>
    <span class="variant-label">E &middot; Inset</span>
    <div class="card v-inset">
      <div class="card-head">
        <div class="avatar">WP</div>
        <div class="card-titles">
          <p class="card-title">Weekly planning</p>
          <p class="card-sub">12 tasks &middot; due Friday</p>
        </div>
      </div>
      <div class="chips">
        <span class="chip">Q2</span>
        <span class="chip olive">Roadmap</span>
      </div>
      <button class="ghost-btn">Open</button>
    </div>
    <p class="variant-note">best for: nested cards inside white panels</p>
  </div>

  <!-- F: Horizontal -->
  <div>
    <span class="variant-label">F &middot; Horizontal</span>
    <div class="card v-horizontal">
      <div class="card-head">
        <div class="avatar">WP</div>
        <div class="card-titles">
          <p class="card-title">Weekly planning</p>
          <p class="card-sub">12 tasks &middot; due Friday</p>
        </div>
      </div>
      <div class="chips">
        <span class="chip">Q2</span>
        <span class="chip olive">Roadmap</span>
      </div>
      <button class="ghost-btn">Open</button>
    </div>
    <p class="variant-note">best for: compact row lists, sidebars</p>
  </div>

</div>
```

---

## 2. Summary Stats Grid

**Name:** Summary Stats Grid

**Description:** A 4-column responsive grid of key-value stat cards using `auto-fill` with `minmax(160px, 1fr)`. Each cell has a monospace uppercase label and a prominent numeric or text value. One value per grid can use the `accent` modifier to draw the eye to the most important stat.

**When to use:** At the top of a post or section to surface at-a-glance metrics — effort, scope, counts, flags. Works well before a detailed explanation. Keep values short (one word, number, or brief phrase).

**Dark mode notes:** Swap cell background to `#12122A`, border to `#2E2E4A`, label color to `#8888AA`, value color to `#F0F0FF`. The `.accent` value color (`--accent`) stays unchanged and provides good contrast on dark surfaces.

```html
<style>
:root {
  --ivory:              #FAF9F5;
  --slate:              #141413;
  --white:              #FFFFFF;
  --gray-300:           #D1CFC5;
  --gray-500:           #87867F;
  --accent:             #5B5FC7;
  --accent-light:       #EEEEFF;
  --accent-dark:        #3D4096;
  --accent-translucent: rgba(91,95,199,0.10);
  --sans: system-ui, -apple-system, sans-serif;
  --mono: ui-monospace, 'SF Mono', Menlo, monospace;
}
* { box-sizing: border-box; margin: 0; padding: 0; }
body { font-family: var(--sans); background: var(--ivory); padding: 32px; -webkit-font-smoothing: antialiased; }

.summary {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
  gap: 16px;
}
.summary .cell {
  background: var(--white);
  border: 1.5px solid var(--gray-300);
  border-radius: 12px;
  padding: 18px 20px;
}
.summary .k {
  font-family: var(--mono);
  font-size: 11px;
  text-transform: uppercase;
  letter-spacing: 0.06em;
  color: var(--gray-500);
  margin-bottom: 6px;
}
.summary .v {
  font-size: 17px;
  color: var(--slate);
  font-weight: 600;
  font-family: var(--sans);
}
.summary .v.accent { color: var(--accent); }
</style>

<div class="summary">
  <div class="cell"><div class="k">Effort</div><div class="v accent">~2 weeks</div></div>
  <div class="cell"><div class="k">Surfaces touched</div><div class="v">3 packages</div></div>
  <div class="cell"><div class="k">New tables</div><div class="v">2</div></div>
  <div class="cell"><div class="k">Feature flag</div><div class="v">feature_v1</div></div>
</div>
```

---

## 3. Comparison Card Grid

**Name:** Approach Comparison Grid

**Description:** A responsive grid of approach cards, each containing: a numbered heading (oat badge + serif title), a short description, a dark code panel, a pro/con tradeoff table (olive dots for pros, accent-colored dots for cons), and a metadata chip strip. The grid uses `repeat(3, minmax(0, 1fr))` and collapses to a single column below 1100px. Each approach card is a self-contained `<article>`.

**When to use:** Side-by-side comparison of 2–4 implementation options. Suited for technical posts comparing architectural approaches, library choices, or algorithm variants. The code panel is optional — omit the `.code` block when comparing non-code options.

**Dark mode notes:** Code panels already use dark slate backgrounds and render unchanged. Card backgrounds: swap to `#12122A`, border to `#2E2E4A`. Tradeoffs table header row: `#1A1A30`. `.kw` keyword color in code panels can remain `--accent` or shift to `#8B8FE8` for softer contrast. Chip fills: `#1E1E38` with `#2E2E4A` border. Pro dot (`--olive`) and con dot (`--accent`) both read well on dark backgrounds.

```html
<style>
:root {
  --ivory:              #FAF9F5;
  --slate:              #141413;
  --oat:                #E3DACC;
  --white:              #FFFFFF;
  --gray-150:           #F0EEE6;
  --gray-300:           #D1CFC5;
  --gray-500:           #87867F;
  --gray-700:           #3D3D3A;
  --olive:              #788C5D;
  --accent:             #5B5FC7;
  --accent-light:       #EEEEFF;
  --accent-dark:        #3D4096;
  --accent-translucent: rgba(91,95,199,0.10);
  --serif: ui-serif, Georgia, 'Times New Roman', serif;
  --sans:  system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
  --mono:  ui-monospace, 'SF Mono', Menlo, Monaco, monospace;
}
* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: var(--sans); background: var(--ivory); color: var(--gray-700); line-height: 1.55; padding: 32px; -webkit-font-smoothing: antialiased; }

/* ── Grid layout ── */
.approaches {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 28px;
}
@media (max-width: 1100px) { .approaches { grid-template-columns: 1fr; } }

/* ── Approach card ── */
.approach {
  background: var(--white);
  border: 1.5px solid var(--gray-300);
  border-radius: 12px;
  padding: 24px;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

/* ── Numbered heading ── */
.approach-head h2 {
  font-family: var(--serif);
  font-weight: 500;
  font-size: 21px;
  color: var(--slate);
  margin-bottom: 6px;
}
.approach-head .num {
  display: inline-block;
  font-family: var(--mono);
  font-size: 12px;
  background: var(--oat);
  color: var(--slate);
  padding: 2px 8px;
  border-radius: 8px;
  margin-right: 8px;
  vertical-align: 3px;
}
.approach-head p { font-size: 14px; color: var(--gray-500); }

/* ── Dark code panel ── */
.code {
  background: var(--slate);
  border-radius: 12px;
  padding: 18px 20px;
  overflow-x: auto;
}
.code pre {
  font-family: var(--mono);
  font-size: 12.5px;
  line-height: 1.65;
  color: #E8E6DE;
  white-space: pre;
}
/* Syntax class mapping:
   .kw  = keywords      → accent (#5B5FC7)
   .str = strings       → olive  (#788C5D)
   .cm  = comments      → gray   (#87867F)
   .fn  = identifiers   → warm gold (#C9B98A)  */
.code .kw  { color: var(--accent); }
.code .str { color: var(--olive); }
.code .cm  { color: var(--gray-500); }
.code .fn  { color: #C9B98A; }

/* ── Pro/con tradeoff table ── */
.tradeoffs {
  border: 1.5px solid var(--gray-300);
  border-radius: 8px;
  overflow: hidden;
  font-size: 13px;
}
.tradeoffs .row { display: grid; grid-template-columns: 1fr 1fr; }
.tradeoffs .row + .row { border-top: 1.5px solid var(--gray-300); }
.tradeoffs .cell { padding: 10px 14px; color: var(--slate); }
.tradeoffs .cell:first-child { border-right: 1.5px solid var(--gray-300); }
.tradeoffs .head {
  background: var(--gray-150);
  font-weight: 600;
  color: var(--slate);
  font-size: 12px;
  text-transform: uppercase;
  letter-spacing: 0.04em;
}
/* Colored dot pseudo-elements */
.tradeoffs .pro::before,
.tradeoffs .con::before {
  content: '';
  display: inline-block;
  width: 6px; height: 6px;
  border-radius: 50%;
  margin-right: 8px;
  vertical-align: 2px;
}
.tradeoffs .pro::before { background: var(--olive); }
.tradeoffs .con::before { background: var(--accent); }

/* ── Metadata chip strip ── */
.chips { display: flex; flex-wrap: wrap; gap: 8px; }
.chip {
  font-family: var(--mono);
  font-size: 11.5px;
  background: var(--gray-150);
  border: 1.5px solid var(--gray-300);
  color: var(--gray-700);
  padding: 5px 10px;
  border-radius: 8px;
  white-space: nowrap;
}
.chip strong { color: var(--slate); font-weight: 600; }
</style>

<section class="approaches">

  <article class="approach">
    <header class="approach-head">
      <h2><span class="num">01</span>Approach One</h2>
      <p>Short description of this approach and when to use it.</p>
    </header>

    <div class="code"><pre><span class="cm">// code goes here</span>
<span class="kw">function</span> <span class="fn">example</span>() {
  <span class="kw">return</span> <span class="str">'result'</span>;
}</pre></div>

    <div class="tradeoffs">
      <div class="row head">
        <div class="cell">Pro</div>
        <div class="cell">Con</div>
      </div>
      <div class="row">
        <div class="cell pro">Zero new abstractions</div>
        <div class="cell con">Logic duplicated everywhere</div>
      </div>
      <div class="row">
        <div class="cell pro">Easy to step through in devtools</div>
        <div class="cell con">Two pieces of state for one value</div>
      </div>
    </div>

    <div class="chips">
      <span class="chip">Bundle: <strong>+0 kb</strong></span>
      <span class="chip">Testability: <strong>medium</strong></span>
      <span class="chip">Reuse: <strong>low</strong></span>
    </div>
  </article>

  <article class="approach">
    <header class="approach-head">
      <h2><span class="num">02</span>Approach Two</h2>
      <p>Short description of this approach and when to use it.</p>
    </header>

    <div class="code"><pre><span class="cm">// code goes here</span>
<span class="kw">function</span> <span class="fn">example</span>() {
  <span class="kw">return</span> <span class="str">'result'</span>;
}</pre></div>

    <div class="tradeoffs">
      <div class="row head">
        <div class="cell">Pro</div>
        <div class="cell">Con</div>
      </div>
      <div class="row">
        <div class="cell pro">Single import reused everywhere</div>
        <div class="cell con">One more file to maintain</div>
      </div>
      <div class="row">
        <div class="cell pro">Trivial to unit test</div>
        <div class="cell con">Generic type hides intent slightly</div>
      </div>
    </div>

    <div class="chips">
      <span class="chip">Bundle: <strong>+0.2 kb</strong></span>
      <span class="chip">Testability: <strong>high</strong></span>
      <span class="chip">Reuse: <strong>high</strong></span>
    </div>
  </article>

  <article class="approach">
    <header class="approach-head">
      <h2><span class="num">03</span>Approach Three</h2>
      <p>Short description of this approach and when to use it.</p>
    </header>

    <div class="code"><pre><span class="cm">// code goes here</span>
<span class="kw">function</span> <span class="fn">example</span>() {
  <span class="kw">return</span> <span class="str">'result'</span>;
}</pre></div>

    <div class="tradeoffs">
      <div class="row head">
        <div class="cell">Pro</div>
        <div class="cell">Con</div>
      </div>
      <div class="row">
        <div class="cell pro">Leading/trailing edge handled</div>
        <div class="cell con">New runtime dependency to audit</div>
      </div>
      <div class="row">
        <div class="cell pro">Well-tested edge cases</div>
        <div class="cell con">Extra kb for something we could own</div>
      </div>
    </div>

    <div class="chips">
      <span class="chip">Bundle: <strong>+1.4 kb</strong></span>
      <span class="chip">Testability: <strong>high</strong></span>
      <span class="chip">Reuse: <strong>high</strong></span>
    </div>
  </article>

</section>
```
