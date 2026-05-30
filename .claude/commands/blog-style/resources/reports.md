# Reports — HTML/CSS Components

Source: `style-reference/08-reports.md` (status reports and incident postmortems).

These components cover two common technical report formats: a **periodic status digest** (metrics, shipped work, carryover) and a **structured incident postmortem** (timeline, root cause, impact, action items). Appropriate for blog posts covering system design retrospectives, performance analysis write-ups, engineering process posts, and incident/outage deep-dives.

**Blog adaptation notes:**
- All components are self-contained via `<style>` blocks in a Hugo `<div class="raw-html-embed">` or inline in the post body.
- Define the shared CSS variables once in a post-level `<style>` block and reuse across all components in that post.
- SVG bar charts render without JavaScript and scale with the container width.
- The fixed TOC requires `position: fixed` — skip or adapt for blog layouts that already provide a TOC sidebar.

---

## Design Tokens

Define once per post at the top of any post using these components.

### Color Palette

| Variable | Value | Role |
|---|---|---|
| `--ivory` | `#FAF9F5` | Page background |
| `--slate` | `#141413` | Primary text, dark surfaces |
| `--accent` | `#5B5FC7` | Accent, highlights, chart peak bars |
| `--accent-light` | `#EEEEFF` | Tinted surface for accent context |
| `--accent-dark` | `#3D4096` | Deeper accent for hover/emphasis |
| `--accent-translucent` | `rgba(91,95,199,0.10)` | Subtle accent tint (overlays, badges) |
| `--oat` | `#E3DACC` | Muted surface, avatar backgrounds, carryover panels |
| `--olive` | `#788C5D` | Positive/resolved state, green deltas |
| `--rust` | `#B04A3F` | High-risk indicator |
| `--gray-100` | `#F0EEE6` | Table header backgrounds, inline code backgrounds |
| `--gray-300` | `#D1CFC5` | Borders, dividers, baseline axis |
| `--gray-500` | `#87867F` | Secondary text, labels, metadata |
| `--gray-700` | `#3D3D3A` | Subdued body text, carry tags |
| `--white` | `#FFFFFF` | Card/panel backgrounds |

### Typography

```css
--serif: ui-serif, Georgia, serif;
--sans:  system-ui, -apple-system, sans-serif;
--mono:  ui-monospace, 'SF Mono', Menlo, monospace;
```

- `--serif`: h1, h2, large stat numbers — weight 500, letter-spacing `-0.01em`
- `--sans`: body text, labels, table headers — base 15px, line-height 1.6
- `--mono`: timestamps, IDs, PR numbers, badges, metadata — uppercase labels 10–13px, letter-spacing 0.06–0.1em

### Shared Base CSS

Drop this block once at the top of any post that uses these components.

```html
<style>
  .rpt {
    --ivory:              #FAF9F5;
    --slate:              #141413;
    --accent:             #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91,95,199,0.10);
    --oat:                #E3DACC;
    --olive:              #788C5D;
    --rust:               #B04A3F;
    --gray-100:           #F0EEE6;
    --gray-300:           #D1CFC5;
    --gray-500:           #87867F;
    --gray-700:           #3D3D3A;
    --white:              #FFFFFF;
    --serif:        ui-serif, Georgia, serif;
    --sans:         system-ui, -apple-system, sans-serif;
    --mono:         ui-monospace, 'SF Mono', Menlo, monospace;
    --radius-panel: 12px;
    --border:       1.5px solid var(--gray-300);

    font-family: var(--sans);
    font-size: 15px;
    line-height: 1.6;
    color: var(--slate);
    -webkit-font-smoothing: antialiased;
    box-sizing: border-box;
  }
  .rpt *, .rpt *::before, .rpt *::after { box-sizing: border-box; }
</style>
```

Wrap each component in `<div class="rpt">...</div>` to scope the variables.

---

## Component 1: Report Header

**Description:** Page title in serif, paired with a monospace auto-generated pill badge and a date/repo metadata line. The pill uses `--accent` as its background with white text, making it visually distinct from the muted gray-100 pills used elsewhere. Flex row with `align-items: baseline` keeps the pill optically anchored to the text cap-height rather than floating at the vertical midpoint.

**When to use:** Opening element of any status report, weekly digest, or periodic summary post. Establishes the report type and time period at a glance.

**Dark mode notes:** The `h1` uses hardcoded `#141413` — override with `color: var(--ivory)` in dark contexts. The auto-pill's white text on accent background holds up well on both light and dark backgrounds.

```html
<style>
  .rpt-header { margin-bottom: 40px; }
  .rpt-header-top {
    display: flex;
    align-items: baseline;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 12px;
    margin-bottom: 8px;
  }
  .rpt-header h1 {
    font-family: ui-serif, Georgia, serif;
    font-weight: 500;
    font-size: 38px;
    letter-spacing: -0.01em;
    margin: 0;
    color: #141413;
  }
  .auto-pill {
    font-family: ui-monospace, 'SF Mono', Menlo, monospace;
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 0.06em;
    color: #FFFFFF;
    background: #5B5FC7;
    border-radius: 999px;
    padding: 5px 11px;
    white-space: nowrap;
  }
  .rpt-date-range {
    color: #87867F;
    font-size: 14px;
  }
  .rpt-date-range .repo {
    font-family: ui-monospace, 'SF Mono', Menlo, monospace;
    font-size: 13px;
    color: #3D3D3A;
  }
</style>

<div class="rpt">
  <div class="rpt-header">
    <div class="rpt-header-top">
      <h1>Engineering Status &mdash; Week 11</h1>
      <span class="auto-pill">auto-generated</span>
    </div>
    <div class="rpt-date-range">
      Mar 10 &ndash; Mar 16, 2025 &nbsp;&middot;&nbsp;
      <span class="repo">acme/app @ main</span>
    </div>
  </div>
</div>
```

---

## Component 2: Incident Header with Meta Pills

**Description:** Incident ID in monospace above a serif title, followed by a flex row of pill badges. Pills have three variants: `sev` (accent background, white text — for severity level), `resolved` (olive background, white text — for status), and `neutral` (gray-100 background with border — for metadata key/value pairs). The neutral pill uses `<span class="k">` for the key (reduced opacity) and `<span class="v">` (monospace) for the value.

**When to use:** Top of any incident postmortem, outage write-up, or SRE retrospective post. The pill row communicates severity, resolution state, and key facts without requiring the reader to scan prose.

**Dark mode notes:** The `sev` pill's accent background reads well on dark surfaces. The `neutral` pill's `#F0EEE6` background will look washed out on dark backgrounds — override with `background: rgba(255,255,255,0.08); border-color: rgba(255,255,255,0.15)`.

```html
<style>
  .inc-id {
    font-family: ui-monospace, 'SF Mono', Menlo, monospace;
    font-size: 13px;
    color: #87867F;
    letter-spacing: 0.02em;
    margin-bottom: 8px;
  }
  .inc-title {
    font-family: ui-serif, Georgia, serif;
    font-weight: 500;
    font-size: 36px;
    letter-spacing: -0.01em;
    margin: 0 0 18px;
    line-height: 1.2;
    color: #141413;
  }
  .meta-row {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    gap: 10px 12px;
  }
  .pill {
    display: inline-flex;
    align-items: baseline;
    gap: 6px;
    font-family: system-ui, -apple-system, sans-serif;
    font-size: 12px;
    font-weight: 600;
    border-radius: 999px;
    padding: 5px 12px;
    line-height: 1;
  }
  .pill .k { font-weight: 400; opacity: 0.75; }
  .pill.sev      { background: #5B5FC7; color: #FFFFFF; letter-spacing: 0.03em; }
  .pill.resolved { background: #788C5D; color: #FFFFFF; }
  .pill.neutral  {
    background: #F0EEE6;
    color: #3D3D3A;
    border: 1.5px solid #D1CFC5;
  }
  .pill.neutral .v {
    font-family: ui-monospace, 'SF Mono', Menlo, monospace;
  }
</style>

<div class="rpt">
  <div class="inc-id">INC-2025-0412</div>
  <h1 class="inc-title">Elevated 502s on task sync</h1>
  <div class="meta-row">
    <span class="pill sev">SEV-2</span>
    <span class="pill resolved">Resolved</span>
    <span class="pill neutral"><span class="k">Duration</span> <span class="v">47 min</span></span>
    <span class="pill neutral"><span class="k">Detected</span> <span class="v">Apr 12 &middot; 14:07</span></span>
    <span class="pill neutral"><span class="k">Owner</span> <span class="v">Devon Park</span></span>
  </div>
</div>
```

**Pill variants for standalone use:**

```html
<!-- Severity badges -->
<span class="pill sev">SEV-1</span>
<span class="pill sev">SEV-2</span>
<span class="pill sev">SEV-3</span>

<!-- Status badge -->
<span class="pill resolved">Resolved</span>

<!-- Key/value metadata -->
<span class="pill neutral"><span class="k">Duration</span> <span class="v">47 min</span></span>
<span class="pill neutral"><span class="k">Region</span> <span class="v">us-east-1</span></span>
```

---

## Component 3: SVG Bar Chart

**Description:** Inline SVG bar chart inside a bordered white panel. Uses `viewBox` + `width: 100%` for full responsiveness — no JavaScript required. Grid lines are `<line>` elements. Bars are `<rect rx="6">` for rounded tops. The peak/highlighted bar uses `--accent` (`#5B5FC7`); standard bars use `--gray-300` (`#D1CFC5`). Value labels appear above each bar; x-axis labels sit below the baseline. The peak bar's value label uses white text (`#FFFFFF`) for contrast against the dark accent fill.

**Coordinate system:** baseline at y=140, each unit = 40px tall, bars are 56px wide with 24px gaps, starting at x=64.

**Scaling:** To adapt for different data ranges, change the unit (px per data unit) and recalculate: `height = value * unit`, `y = baseline_y - height`. The baseline is always fixed at the bottom of the plotting area.

**When to use:** Displaying time-series metrics, per-day/per-week counts, or any small dataset (5–10 bars) where a pure-SVG approach is preferable to a JavaScript charting library. Works inline in any blog post without external dependencies.

**Dark mode notes:** Grid lines use hardcoded `#F0EEE6` and `#D1CFC5` — swap to `rgba(255,255,255,0.08)` and `rgba(255,255,255,0.2)` on dark surfaces. The accent bar color (`#5B5FC7`) holds contrast on both light and dark panel backgrounds. Standard bars can switch from `#D1CFC5` to `rgba(255,255,255,0.15)`.

```html
<style>
  .chart-panel {
    background: #FFFFFF;
    border: 1.5px solid #D1CFC5;
    border-radius: 12px;
    padding: 24px 28px 18px;
  }
  .chart-panel svg {
    display: block;
    width: 100%;
    height: auto;
  }
  .chart-caption {
    font-size: 12px;
    color: #87867F;
    margin-top: 12px;
    font-family: system-ui, -apple-system, sans-serif;
  }
</style>

<div class="rpt">
  <div class="chart-panel">
    <svg viewBox="0 0 640 180" role="img" aria-label="PRs merged per day">
      <!-- grid lines -->
      <line x1="48" y1="20"  x2="620" y2="20"  stroke="#F0EEE6" stroke-width="1"/>
      <line x1="48" y1="60"  x2="620" y2="60"  stroke="#F0EEE6" stroke-width="1"/>
      <line x1="48" y1="100" x2="620" y2="100" stroke="#F0EEE6" stroke-width="1"/>
      <!-- baseline -->
      <line x1="48" y1="140" x2="620" y2="140" stroke="#D1CFC5" stroke-width="1.5"/>
      <!-- y-axis labels -->
      <text x="40" y="144" text-anchor="end" font-family="system-ui" font-size="11" fill="#87867F">0</text>
      <text x="40" y="104" text-anchor="end" font-family="system-ui" font-size="11" fill="#87867F">1</text>
      <text x="40" y="64"  text-anchor="end" font-family="system-ui" font-size="11" fill="#87867F">2</text>
      <text x="40" y="24"  text-anchor="end" font-family="system-ui" font-size="11" fill="#87867F">3</text>
      <!-- bars: baseline y=140, unit=40px, width=56, gap=24, start x=64 -->
      <!-- Mon:2 --> <rect x="64"  y="60"  width="56" height="80"  rx="6" fill="#D1CFC5"/>
      <!-- Tue:3 --> <rect x="144" y="20"  width="56" height="120" rx="6" fill="#D1CFC5"/>
      <!-- Wed:1 --> <rect x="224" y="100" width="56" height="40"  rx="6" fill="#D1CFC5"/>
      <!-- Thu:4 (peak) --> <rect x="304" y="0"   width="56" height="140" rx="6" fill="#5B5FC7"/>
      <!-- Fri:2 --> <rect x="384" y="60"  width="56" height="80"  rx="6" fill="#D1CFC5"/>
      <!-- Sat:1 --> <rect x="464" y="100" width="56" height="40"  rx="6" fill="#D1CFC5"/>
      <!-- Sun:1 --> <rect x="544" y="100" width="56" height="40"  rx="6" fill="#D1CFC5"/>
      <!-- value labels -->
      <text x="92"  y="54"  text-anchor="middle" font-family="system-ui" font-size="11" fill="#87867F">2</text>
      <text x="172" y="14"  text-anchor="middle" font-family="system-ui" font-size="11" fill="#87867F">3</text>
      <text x="252" y="94"  text-anchor="middle" font-family="system-ui" font-size="11" fill="#87867F">1</text>
      <text x="332" y="14"  text-anchor="middle" font-family="system-ui" font-size="11" fill="#FFFFFF" font-weight="600">4</text>
      <text x="412" y="54"  text-anchor="middle" font-family="system-ui" font-size="11" fill="#87867F">2</text>
      <text x="492" y="94"  text-anchor="middle" font-family="system-ui" font-size="11" fill="#87867F">1</text>
      <text x="572" y="94"  text-anchor="middle" font-family="system-ui" font-size="11" fill="#87867F">1</text>
      <!-- x-axis labels -->
      <text x="92"  y="158" text-anchor="middle" font-family="system-ui" font-size="11" fill="#87867F">Mon</text>
      <text x="172" y="158" text-anchor="middle" font-family="system-ui" font-size="11" fill="#87867F">Tue</text>
      <text x="252" y="158" text-anchor="middle" font-family="system-ui" font-size="11" fill="#87867F">Wed</text>
      <text x="332" y="158" text-anchor="middle" font-family="system-ui" font-size="11" fill="#87867F">Thu</text>
      <text x="412" y="158" text-anchor="middle" font-family="system-ui" font-size="11" fill="#87867F">Fri</text>
      <text x="492" y="158" text-anchor="middle" font-family="system-ui" font-size="11" fill="#87867F">Sat</text>
      <text x="572" y="158" text-anchor="middle" font-family="system-ui" font-size="11" fill="#87867F">Sun</text>
    </svg>
    <div class="chart-caption">
      PRs merged per day. Thursday spike is the bulk-edit feature train (4 PRs landed together).
    </div>
  </div>
</div>
```

---

## Component 4: Action Items Checklist

**Description:** Grid-based action item list inside a white bordered panel. Each row is a 4-column grid: checkbox, avatar circle, description text, due date. The `.done` variant fills the checkbox with olive green and renders a CSS checkmark via `::after` (rotated border trick — no SVG or icon font needed). Completed items get `text-decoration: line-through` with `text-decoration-color` set to `--gray-300` (lighter than the text color, so the strikethrough reads as secondary). Avatar circles are 30px with `--oat` background and 11px bold initials.

**CSS checkmark pattern:** `border: solid white; border-width: 0 2px 2px 0; transform: rotate(40deg)` creates a checkmark from two border sides of a rotated rectangle.

**When to use:** Action items section of an incident postmortem, sprint retrospective, or any post that tracks follow-up work with owners and due dates. The `.done` state works well for showing progress within a published post.

**Dark mode notes:** The white panel background needs an override to a dark surface. The olive-filled checkbox (`#788C5D`) remains readable on dark backgrounds. Avatar oat background (`#E3DACC`) will appear too light on dark surfaces — swap to `rgba(255,255,255,0.12)`.

```html
<style>
  .actions {
    background: #FFFFFF;
    border: 1.5px solid #D1CFC5;
    border-radius: 12px;
    overflow: hidden;
  }
  .ai-row {
    display: grid;
    grid-template-columns: 36px 36px 1fr 96px;
    align-items: center;
    gap: 14px;
    padding: 14px 18px;
    border-bottom: 1px solid #F0EEE6;
    font-family: system-ui, -apple-system, sans-serif;
  }
  .ai-row:last-child { border-bottom: none; }
  .ai-check {
    width: 18px;
    height: 18px;
    border: 1.5px solid #D1CFC5;
    border-radius: 5px;
    background: #FFFFFF;
    position: relative;
    flex-shrink: 0;
  }
  .ai-row.done .ai-check {
    background: #788C5D;
    border-color: #788C5D;
  }
  .ai-row.done .ai-check::after {
    content: "";
    position: absolute;
    left: 4px;
    top: 1px;
    width: 5px;
    height: 9px;
    border: solid #FFFFFF;
    border-width: 0 2px 2px 0;
    transform: rotate(40deg);
  }
  .ai-row.done .ai-desc {
    color: #87867F;
    text-decoration: line-through;
    text-decoration-color: #D1CFC5;
  }
  .ai-avatar {
    width: 30px;
    height: 30px;
    border-radius: 50%;
    background: #E3DACC;
    color: #3D3D3A;
    font-size: 11px;
    font-weight: 600;
    display: flex;
    align-items: center;
    justify-content: center;
    letter-spacing: 0.02em;
  }
  .ai-desc {
    font-size: 14px;
    color: #141413;
  }
  .ai-desc code {
    font-family: ui-monospace, 'SF Mono', Menlo, monospace;
    font-size: 13px;
    background: #F0EEE6;
    padding: 1.5px 5px;
    border-radius: 4px;
  }
  .ai-due {
    font-family: ui-monospace, 'SF Mono', Menlo, monospace;
    font-size: 12px;
    color: #87867F;
    text-align: right;
  }
</style>

<div class="rpt">
  <div class="actions">

    <div class="ai-row done">
      <span class="ai-check"></span>
      <span class="ai-avatar" title="Devon Park">DP</span>
      <span class="ai-desc">Revert cfg-9a12 and restore pool limit to 64</span>
      <span class="ai-due">Apr 12</span>
    </div>

    <div class="ai-row">
      <span class="ai-check"></span>
      <span class="ai-avatar" title="Mira Okafor">MO</span>
      <span class="ai-desc">Add config-linter range check for <code>max_connections</code> (warn &lt; 32)</span>
      <span class="ai-due">Apr 18</span>
    </div>

    <div class="ai-row">
      <span class="ai-check"></span>
      <span class="ai-avatar" title="Sam Reyes">SR</span>
      <span class="ai-desc">Surface recent config rollouts alongside deploys in the on-call dashboard</span>
      <span class="ai-due">Apr 25</span>
    </div>

    <div class="ai-row">
      <span class="ai-check"></span>
      <span class="ai-avatar" title="Priya Anand">PA</span>
      <span class="ai-desc">Canary config changes to one worker AZ for 10 min before fleet-wide promote</span>
      <span class="ai-due">May 02</span>
    </div>

  </div>
</div>
```

**Without avatars (simpler layout):** Remove the `.ai-avatar` column and update the grid to `grid-template-columns: 36px 1fr 96px`.

---

## Component 5: Carryover / Status Panel

**Description:** Oat-background (`#E3DACC`) panel with a list of blocked/slipped/in-review items. Each item is a flex row with a monospace status tag on an ivory (`#FAF9F5`) background and a body text area. Items are separated by `rgba(20,20,19,0.08)` translucent dark borders rather than solid colors, which reads naturally on the warm oat surface and adapts if the background color changes. An optional `.who` span in muted gray-500 places owner attribution inline after the description.

**When to use:** Carryover section of a weekly status report, sprint review, or any post tracking work that slipped or is waiting on external action. The oat background distinguishes this panel from the white-background tables and charts nearby.

**Dark mode notes:** The oat background is a light warm tone — use `background: rgba(255,255,255,0.06)` or a dark warm neutral on dark surfaces. The ivory tag background (`#FAF9F5`) should be replaced with `rgba(255,255,255,0.10)`. The translucent divider technique automatically adapts as long as light/dark is applied consistently.

```html
<style>
  .carryover {
    background: #E3DACC;
    border-radius: 12px;
    padding: 20px 22px;
  }
  .carry-item {
    display: flex;
    align-items: baseline;
    gap: 14px;
    padding: 8px 0;
  }
  .carry-item + .carry-item {
    border-top: 1px solid rgba(20, 20, 19, 0.08);
  }
  .carry-tag {
    font-family: ui-monospace, 'SF Mono', Menlo, monospace;
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 0.05em;
    color: #3D3D3A;
    background: #FAF9F5;
    border-radius: 4px;
    padding: 3px 7px;
    flex-shrink: 0;
  }
  .carry-body {
    font-size: 14px;
    color: #3D3D3A;
    font-family: system-ui, -apple-system, sans-serif;
  }
  .carry-body .who {
    color: #87867F;
    font-size: 12px;
  }
</style>

<div class="rpt">
  <div class="carryover">
    <div class="carry-item">
      <span class="carry-tag">In review</span>
      <div class="carry-body">
        Workspace export to CSV &mdash; waiting on pagination review.
        <span class="who">&middot; Sam Reyes</span>
      </div>
    </div>
    <div class="carry-item">
      <span class="carry-tag">Blocked</span>
      <div class="carry-body">
        SSO group mapping &mdash; blocked on staging IdP credentials from IT.
        <span class="who">&middot; Priya Anand</span>
      </div>
    </div>
    <div class="carry-item">
      <span class="carry-tag">Slipped</span>
      <div class="carry-body">
        Mobile push reliability dashboard &mdash; deprioritized for incident follow-up.
        <span class="who">&middot; Devon Park</span>
      </div>
    </div>
  </div>
</div>
```

**Custom tag colors:** To add semantic color to tags (e.g., red for blocked), add a modifier class: `.carry-tag.blocked { background: #B04A3F; color: #FFFFFF; }`.

---

## Component 6: Highlights List

**Description:** Unstyled list with custom square bullet markers using the CSS `::before` pseudo-element. The marker is a 7x7px square with `border-radius: 2px` and `--accent` fill (`#5B5FC7`), positioned absolutely at `left: 6px`. The 26px left padding on each `<li>` provides the indent. Bold text within items uses `color: #141413` (slate) to pop against the muted `#3D3D3A` body text. The square marker visually distinguishes this list from standard prose bullets.

**When to use:** Key highlights or notable achievements section in a status report, sprint summary, or retrospective post. Appropriate when each item leads with a bolded outcome followed by brief supporting context.

**Dark mode notes:** The accent square (`#5B5FC7`) provides good contrast on dark backgrounds without changes. Body text color `#3D3D3A` will be too dark on dark surfaces — override `.highlights li` with `color: var(--gray-300)` and `.highlights li strong` with `color: var(--ivory)`.

```html
<style>
  .highlights {
    list-style: none;
    margin: 0;
    padding: 0;
  }
  .highlights li {
    position: relative;
    padding: 0 0 14px 26px;
    font-size: 15px;
    color: #3D3D3A;
  }
  .highlights li::before {
    content: "";
    position: absolute;
    left: 6px;
    top: 8px;
    width: 7px;
    height: 7px;
    border-radius: 2px;
    background: #5B5FC7;
  }
  .highlights li strong {
    color: #141413;
    font-weight: 600;
  }
</style>

<div class="rpt">
  <ul class="highlights">
    <li>
      <strong>Bulk task editing shipped to 100%.</strong> The multi-select
      toolbar landed behind a flag on Monday and ramped to all workspaces by
      Thursday with no error-rate regression.
    </li>
    <li>
      <strong>Sync API p95 down 38%.</strong> Replacing per-task auth checks
      with a scoped batch lookup cut the hot path from 410ms to 255ms on the
      staging load test.
    </li>
    <li>
      <strong>One SEV-2 on Wednesday</strong> &mdash; a config rollout pushed
      a bad connection-pool limit. Mitigated in 47 minutes.
    </li>
  </ul>
</div>
```

---

## Design Patterns Reference

### Rounded Table Technique

To get `border-radius` on a `<table>` (which normally ignores it):

```css
table {
  border-collapse: separate;  /* not collapse — collapse kills radius */
  border-spacing: 0;          /* visually equivalent to collapse */
  border-radius: 12px;
  overflow: hidden;           /* clips header corners */
}
```

### Translucent Dividers on Colored Backgrounds

Use `rgba(20, 20, 19, 0.08)` instead of a hard-coded gray for dividers between items on the oat background. This adapts automatically if the background color changes and avoids the flat appearance of solid dividers on warm tones.

### CSS Checkmark Pattern

Pure CSS, no SVG or icon font:

```css
.check::after {
  content: "";
  position: absolute;
  left: 4px;
  top: 1px;
  width: 5px;
  height: 9px;
  border: solid white;
  border-width: 0 2px 2px 0;   /* right and bottom borders only */
  transform: rotate(40deg);     /* rotate into checkmark shape */
}
```

### Baseline-Aligned Flex Header

Use `align-items: baseline` (not `center`) when mixing a large serif heading with a small pill badge. The pill stays optically anchored to the text cap-height rather than floating at the vertical midpoint.

### Strikethrough with Separate Decoration Color

```css
.done-text {
  color: #87867F;
  text-decoration: line-through;
  text-decoration-color: #D1CFC5;  /* lighter than text color */
}
```

### Dark Surface Inline Code

On dark backgrounds, use semi-transparent white for code backgrounds instead of a hard-coded color:

```css
.dark-surface code {
  background: rgba(250, 249, 245, 0.12);
}
```
