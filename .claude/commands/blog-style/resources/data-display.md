# Data Display Components

Design tokens and six copy-paste-ready HTML+CSS components for structured data presentation in blog posts. All components share the same color system and can be combined freely.

---

## Accent Color Tokens

These components use electric indigo as the primary accent in place of clay. Add these tokens alongside the shared base tokens when using components from this file.

```css
--accent:             #5B5FC7;  /* electric indigo — primary accent */
--accent-light:       #EEEEFF;  /* tinted surface for accent backgrounds */
--accent-dark:        #3D4096;  /* deeper indigo for hover/emphasis */
--accent-translucent: rgba(91, 95, 199, 0.10);  /* ghost backgrounds */
```

The shared palette these components also rely on:

```css
--ivory:    #FAF9F5;
--slate:    #141413;
--oat:      #E3DACC;
--olive:    #788C5D;   /* success / low severity */
--warning:  #C78E3F;   /* warning / medium severity */
--rust:     #B04A3F;   /* danger / high severity */
--gray-100: #F0EEE6;
--gray-300: #D1CFC5;
--gray-500: #87867F;
--gray-700: #3D3D3A;
--white:    #FFFFFF;
--serif:    ui-serif, Georgia, serif;
--sans:     system-ui, -apple-system, sans-serif;
--mono:     ui-monospace, 'SF Mono', Menlo, monospace;
```

---

## 1. Status Badges

**Name:** Status Badge Pills
**Description:** Pill-shaped inline labels for conveying semantic state. Four variants use translucent tinted backgrounds (not solid fills) so the badge reads as a tint rather than a block of color.
**When to use:** Tag items with workflow state (active, pending, deprecated, informational). Sprinkle inline in prose or table cells.

**Variants:**
- **Success** (olive): positive outcome, live, passing
- **Warning** (amber): pending, needs attention, degraded
- **Error** (rust): failed, critical, blocked
- **Info** (indigo accent): neutral informational, in-progress, note

**Dark mode notes:** The translucent backgrounds adapt well on dark surfaces since they layer over the background color. On very dark backgrounds, increase the alpha slightly (e.g. `rgba(120,140,93,0.25)` for olive) if contrast is insufficient.

```html
<style>
  .badge {
    display: inline-flex;
    align-items: center;
    height: 22px;
    padding: 0 9px;
    font-size: 12px;
    font-weight: 500;
    font-family: system-ui, -apple-system, sans-serif;
    border-radius: 999px;
    white-space: nowrap;
  }
  .badge-success { background: rgba(120, 140, 93,  0.16); color: #788C5D; }
  .badge-warning { background: rgba(199, 142, 63,  0.16); color: #A06A2A; }
  .badge-error   { background: rgba(176,  74,  63, 0.16); color: #B04A3F; }
  .badge-info    { background: rgba(91,   95, 199, 0.14); color: #5B5FC7; }
</style>

<div style="display:flex; flex-wrap:wrap; align-items:center; gap:10px; padding:20px; background:#FFFFFF; border:1.5px solid #D1CFC5; border-radius:12px;">
  <span class="badge badge-success">Live</span>
  <span class="badge badge-warning">Pending</span>
  <span class="badge badge-error">Failed</span>
  <span class="badge badge-info">In progress</span>
</div>
```

---

## 2. Severity / Risk Badges

**Name:** Severity Badges
**Description:** Solid-background rectangular badges for structured severity labeling. Uppercase monospace text, fully saturated backgrounds — visually heavier than status badges by design. Used where severity needs to read immediately at a glance.
**When to use:** Risk tables, incident reports, vulnerability listings, any context where HIGH/MED/LOW severity must be scannable.

**Variants:**
- **HIGH** (`rust` — `#B04A3F`): critical, blocking, must-fix
- **MED** (`warning` — `#C78E3F`): notable, investigate, degraded
- **LOW** (`olive` — `#788C5D`): informational, low impact

**Dark mode notes:** On dark backgrounds, wrap in a scoped container and slightly adjust background lightness. The solid fills here are chosen to remain legible against both ivory and white card surfaces; on pure black backgrounds they may need a 1px border for separation.

```html
<style>
  .sev-badge {
    display: inline-block;
    font-family: ui-monospace, 'SF Mono', Menlo, monospace;
    font-size: 11px;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: 0.05em;
    padding: 3px 9px;
    border-radius: 6px;
    white-space: nowrap;
  }
  .sev-high { background: #F3D9CC; color: #8A3B1E; }
  .sev-med  { background: #F5E8CC; color: #7A5018; }
  .sev-low  { background: #E4E9DC; color: #4B5C39; }
</style>

<div style="display:flex; flex-wrap:wrap; align-items:center; gap:10px; padding:20px; background:#FFFFFF; border:1.5px solid #D1CFC5; border-radius:12px;">
  <span class="sev-badge sev-high">HIGH</span>
  <span class="sev-badge sev-med">MED</span>
  <span class="sev-badge sev-low">LOW</span>
</div>
```

---

## 3. Metric Cards

**Name:** Metric Stat Cards
**Description:** Key-value stat display with a large serif number, uppercase label, and an optional delta line showing change direction. The `.warn` variant adds a 4px left border in the accent color to flag a problem metric. The `.delta-up` class is olive (positive), `.delta-down` is rust (negative), and `.delta-flat` is muted gray.
**When to use:** Summary bands at the top of a report, benchmarking sections, before/after performance comparisons, any post quantifying outcomes.

**Dark mode notes:** The white card background will need to become a dark surface token (e.g. `#1F1E1B`). The delta colors (olive, rust) remain readable on dark backgrounds. The `.warn` left border should stay as-is since it is semantic, not decorative.

```html
<style>
  .metric-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 14px;
  }
  @media (max-width: 720px) {
    .metric-grid { grid-template-columns: repeat(2, 1fr); }
  }
  .metric-card {
    background: #FFFFFF;
    border: 1.5px solid #D1CFC5;
    border-radius: 12px;
    padding: 20px 22px 18px;
  }
  .metric-card.warn {
    border-left: 4px solid #5B5FC7;
    padding-left: 19px;
  }
  .metric-num {
    font-family: ui-serif, Georgia, serif;
    font-size: 44px;
    font-weight: 500;
    line-height: 1;
    color: #141413;
    margin-bottom: 8px;
  }
  .metric-label {
    font-family: system-ui, -apple-system, sans-serif;
    font-size: 12px;
    text-transform: uppercase;
    letter-spacing: 0.05em;
    color: #87867F;
  }
  .metric-delta {
    font-family: ui-monospace, 'SF Mono', Menlo, monospace;
    font-size: 11px;
    margin-top: 6px;
    display: flex;
    align-items: center;
    gap: 4px;
  }
  .delta-up   { color: #788C5D; }
  .delta-down { color: #B04A3F; }
  .delta-flat { color: #87867F; }
</style>

<div class="metric-grid">
  <div class="metric-card">
    <div class="metric-num">14</div>
    <div class="metric-label">PRs merged</div>
    <div class="metric-delta delta-up">&#x2191; +3 vs prior period</div>
  </div>
  <div class="metric-card">
    <div class="metric-num">6</div>
    <div class="metric-label">Deploys</div>
    <div class="metric-delta delta-flat">&plusmn;0 unchanged</div>
  </div>
  <div class="metric-card warn">
    <div class="metric-num">1</div>
    <div class="metric-label">Incidents</div>
    <div class="metric-delta delta-down">&#x2193; SEV-2 &middot; 47m</div>
  </div>
  <div class="metric-card">
    <div class="metric-num">99%</div>
    <div class="metric-label">Test pass rate</div>
    <div class="metric-delta delta-up">&#x2191; +1.1pp</div>
  </div>
</div>
```

---

## 4. Comparison Table

**Name:** Comparison Table
**Description:** A bordered table with monospace uppercase column headers and semantic `.good` / `.bad` cell coloring. Uses `border-collapse: separate` with `border-spacing: 0` to enable `border-radius` on the container. The header row is de-emphasized so the data rows carry focus.
**When to use:** Putting two approaches, tools, or configurations side by side with explicit favorable/unfavorable encoding. CS concept explainers, tool comparisons, architecture decision records.

**Cell states:**
- `.good` — olive text (`#788C5D`): favorable property
- `.bad` — rust text (`#B04A3F`): unfavorable property
- No class — default gray-700: neutral

**Dark mode notes:** On dark backgrounds, change the `background: #FFFFFF` on the table to your dark surface color, and change `border-bottom: 1px solid #F0EEE6` row dividers to a more visible dark separator (e.g. `rgba(255,255,255,0.06)`). The `.good` and `.bad` colors hold on dark surfaces.

```html
<style>
  table.cmp {
    width: 100%;
    max-width: 640px;
    border-collapse: separate;
    border-spacing: 0;
    background: #FFFFFF;
    border: 1.5px solid #D1CFC5;
    border-radius: 12px;
    overflow: hidden;
    font-size: 13.5px;
    font-family: system-ui, -apple-system, sans-serif;
  }
  table.cmp thead th {
    text-align: left;
    font-family: ui-monospace, 'SF Mono', Menlo, monospace;
    font-size: 11px;
    font-weight: 500;
    text-transform: uppercase;
    letter-spacing: 0.06em;
    color: #87867F;
    background: #F0EEE6;
    padding: 11px 14px;
    border-bottom: 1.5px solid #D1CFC5;
  }
  table.cmp tbody td {
    padding: 10px 14px;
    border-bottom: 1px solid #F0EEE6;
    color: #3D3D3A;
    vertical-align: middle;
  }
  table.cmp tbody tr:last-child td { border-bottom: none; }
  table.cmp tbody tr:hover { background: #FAF9F5; }
  table.cmp td.good { color: #788C5D; }
  table.cmp td.bad  { color: #B04A3F; }
</style>

<table class="cmp">
  <thead>
    <tr>
      <th>Property</th>
      <th>Approach A</th>
      <th>Approach B</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Keys moved on node add</td>
      <td class="bad">~(N&minus;1)/N of all keys</td>
      <td class="good">~1/(N+1)</td>
    </tr>
    <tr>
      <td>Hot-spot risk</td>
      <td class="good">Even by construction</td>
      <td>Uneven — fix with virtual nodes</td>
    </tr>
    <tr>
      <td>Lookup cost</td>
      <td class="good">O(1)</td>
      <td>O(log N) binary search on ring</td>
    </tr>
    <tr>
      <td>Operational complexity</td>
      <td class="good">Simple</td>
      <td class="bad">Higher — requires ring management</td>
    </tr>
  </tbody>
</table>
```

---

## 5. Progress Indicators

**Name:** Progress Bar
**Description:** Horizontal filled bar showing a percentage value, with a text label above. The filled track uses the accent color. The track background is `--gray-100`. A muted percentage label sits to the right of the bar name. Works for any 0–100 value.
**When to use:** Test coverage, benchmark results, capacity utilization, completion rates, any metric expressed as a proportion.

**Variants:** A single bar stands alone; stack multiple `.progress-item` rows for a group (e.g. language breakdown, feature coverage per component). Optionally tint filled tracks with different semantic colors (olive for passing, rust for failing) by overriding `--bar-color`.

**Dark mode notes:** Set `--bar-track` to a darker surface like `#2A2928`. The accent fill color remains highly visible on dark backgrounds.

```html
<style>
  .progress-group {
    display: flex;
    flex-direction: column;
    gap: 12px;
    max-width: 480px;
  }
  .progress-item {
    display: flex;
    flex-direction: column;
    gap: 5px;
  }
  .progress-header {
    display: flex;
    justify-content: space-between;
    align-items: baseline;
  }
  .progress-label {
    font-family: system-ui, -apple-system, sans-serif;
    font-size: 13px;
    color: #3D3D3A;
  }
  .progress-pct {
    font-family: ui-monospace, 'SF Mono', Menlo, monospace;
    font-size: 11px;
    color: #87867F;
  }
  .progress-track {
    height: 7px;
    background: #F0EEE6;
    border-radius: 999px;
    overflow: hidden;
  }
  .progress-fill {
    height: 100%;
    border-radius: 999px;
    background: #5B5FC7;
    transition: width 0.3s ease;
  }
  /* Semantic color overrides */
  .progress-fill.good { background: #788C5D; }
  .progress-fill.warn { background: #C78E3F; }
  .progress-fill.bad  { background: #B04A3F; }
</style>

<div class="progress-group">
  <div class="progress-item">
    <div class="progress-header">
      <span class="progress-label">Test coverage</span>
      <span class="progress-pct">91%</span>
    </div>
    <div class="progress-track">
      <div class="progress-fill" style="width:91%"></div>
    </div>
  </div>
  <div class="progress-item">
    <div class="progress-header">
      <span class="progress-label">Cache hit rate</span>
      <span class="progress-pct">67%</span>
    </div>
    <div class="progress-track">
      <div class="progress-fill" style="width:67%"></div>
    </div>
  </div>
  <div class="progress-item">
    <div class="progress-header">
      <span class="progress-label">Error budget remaining</span>
      <span class="progress-pct">23%</span>
    </div>
    <div class="progress-track">
      <div class="progress-fill bad" style="width:23%"></div>
    </div>
  </div>
</div>
```

---

## 6. Risk Dot

**Name:** Risk Dot
**Description:** A 9px filled circle used as an inline severity indicator alongside text. Three levels: low (olive), med (warning/amber), high (rust). Designed to sit inside table cells, list items, or inline in prose without adding vertical height.
**When to use:** Shipped-items tables, changelog entries, anywhere a severity needs to be indicated inline without a full badge taking up space. Often paired with a short text label ("Low", "Med", "High") but works as a visual-only indicator too.

**Dark mode notes:** The three semantic fill colors (olive, amber, rust) are readable on both light and dark backgrounds. No changes needed for dark mode.

```html
<style>
  .risk-inline {
    display: inline-flex;
    align-items: center;
    gap: 7px;
    font-size: 12px;
    color: #87867F;
    font-family: system-ui, -apple-system, sans-serif;
  }
  .risk-dot {
    width: 9px;
    height: 9px;
    border-radius: 50%;
    flex-shrink: 0;
    display: inline-block;
  }
  .risk-dot.low  { background: #788C5D; }
  .risk-dot.med  { background: #C78E3F; }
  .risk-dot.high { background: #B04A3F; }
</style>

<!-- Standalone inline usage -->
<div style="display:flex; flex-wrap:wrap; align-items:center; gap:16px; padding:20px; background:#FFFFFF; border:1.5px solid #D1CFC5; border-radius:12px;">
  <span class="risk-inline"><span class="risk-dot low"></span>Low</span>
  <span class="risk-inline"><span class="risk-dot med"></span>Med</span>
  <span class="risk-inline"><span class="risk-dot high"></span>High</span>
</div>

<!-- In a table cell (example row) -->
<!--
<td><span class="risk-inline"><span class="risk-dot high"></span>High</span></td>
-->
```

---

## Usage Notes

### Combining components

Metric cards + risk dots: place `.risk-inline` in the `.metric-delta` line to annotate a stat card with a severity level without adding a full badge.

Comparison table + status badges: add a `.badge` inside a `<td>` for columns that carry status rather than text values (e.g. a "Support" column with "Live" / "Deprecated").

Progress bars + metric cards: metric cards give point-in-time values; progress bars communicate proportion. Use both together when a post covers both absolute counts and relative coverage.

### Scoping styles

All class names in this file are specific enough to avoid collisions with PaperMod's default styles. If you use multiple components in one post, a single shared `<style>` block at the top of the post can hold all the CSS, with the HTML components placed inline throughout the body.

### Responsive behavior

All components are designed for prose-width containers (~640–860px). The metric grid collapses to 2 columns at 720px. Progress bars and risk dots are inherently flexible. The comparison table uses `max-width: 640px` by default — remove this constraint if you need it full-width.
