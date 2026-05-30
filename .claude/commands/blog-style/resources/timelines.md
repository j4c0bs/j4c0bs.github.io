# Timelines — HTML/CSS Component Reference

Three self-contained timeline components adapted from the blog design system. Each block is copy-paste ready for embedding in a Hugo post via raw HTML passthrough.

All three components use `--accent: #5B5FC7` (electric indigo) as the primary accent in place of the original clay/orange. The additional accent sub-tokens below are defined in every component's `:root` block.

---

## Accent Token Reference

| Token | Value | Role |
|---|---|---|
| `--accent` | `#5B5FC7` | Primary accent — active dots, connector lines, highlighted states |
| `--accent-light` | `#EEEEFF` | Light fill for accent-tinted surfaces (hover backgrounds, banners) |
| `--accent-dark` | `#3D4096` | Darker accent for pressed states or text on light accent surfaces |
| `--accent-translucent` | `rgba(91,95,199,0.10)` | Translucent accent for subtle washes and ghost hover fills |

These tokens sit alongside the shared palette tokens (`--ivory`, `--slate`, `--oat`, `--olive`, `--gray-*`). Define them once at the top of a post-level `<style>` block when using multiple components on the same page.

---

## Component 1: Vertical Milestone Timeline

**Name:** Milestone Timeline

**Description:** A three-column grid layout — timestamp column on the left, a dot-and-connector column in the center, and a card body on the right. Each milestone has a colored dot, timestamp, heading, description text, and optional tag chips. The dot style communicates state: solid accent dot for the active/current milestone, solid olive dot for completed milestones, and a hollow accent-bordered dot for upcoming ones. The connecting vertical line hides on the last item.

**When to use:** Implementation plans, project roadmaps, feature development sprints, any multi-step sequential process where order and timing matter. Works well in posts describing how a system was built in phases, or what a planned rollout looks like.

**Dark mode notes:** The dot uses `background: var(--white)` for the hollow state — swap to the dark surface color (e.g. `#1F1E1B`) if embedding inside a dark panel. The `--gray-300` connector line reads clearly on both `--ivory` and white card surfaces. The tag chips use `--gray-150` fill which stays legible in both contexts.

```html
<style>
:root {
  --ivory: #FAF9F5;
  --slate: #141413;
  --accent: #5B5FC7;
  --accent-light: #EEEEFF;
  --accent-dark: #3D4096;
  --accent-translucent: rgba(91,95,199,0.10);
  --oat: #E3DACC;
  --olive: #788C5D;
  --gray-150: #F0EEE6;
  --gray-300: #D1CFC5;
  --gray-500: #87867F;
  --gray-700: #3D3D3A;
  --white: #FFFFFF;
  --serif: ui-serif, Georgia, 'Times New Roman', serif;
  --sans: system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
  --mono: ui-monospace, 'SF Mono', Menlo, Monaco, monospace;
}
* { margin: 0; padding: 0; box-sizing: border-box; }
body {
  font-family: var(--sans);
  background: var(--ivory);
  color: var(--gray-700);
  line-height: 1.55;
  padding: 32px;
  -webkit-font-smoothing: antialiased;
}

.milestones { display: flex; flex-direction: column; gap: 0; }
.milestone { display: grid; grid-template-columns: 120px 28px 1fr; gap: 0 18px; }

/* Timestamp column */
.milestone .when {
  text-align: right;
  font-family: var(--mono);
  font-size: 12px;
  color: var(--gray-500);
  padding-top: 4px;
}

/* Dot + line column */
.milestone .dot-col { display: flex; flex-direction: column; align-items: center; }

/* Default: hollow dot with accent border (upcoming) */
.milestone .dot {
  width: 14px;
  height: 14px;
  border-radius: 50%;
  background: var(--white);
  border: 3px solid var(--accent);
  margin-top: 4px;
  flex-shrink: 0;
}

/* Completed milestone: solid olive fill */
.milestone .dot.done {
  background: var(--olive);
  border-color: var(--olive);
}

/* Active / current milestone: solid accent fill */
.milestone .dot.active {
  background: var(--accent);
  border-color: var(--accent);
  box-shadow: 0 0 0 3px var(--accent-translucent);
}

.milestone .line {
  width: 2px;
  flex: 1;
  background: var(--gray-300);
  margin: 4px 0;
}
.milestone:last-child .line { display: none; }

/* Card body column */
.milestone .body { padding-bottom: 36px; }
.milestone .body h3 {
  font-family: var(--serif);
  font-weight: 500;
  font-size: 19px;
  color: var(--slate);
  margin-bottom: 4px;
}
.milestone .body p {
  font-size: 14px;
  color: var(--gray-500);
  margin-bottom: 10px;
  max-width: 620px;
}

/* Active milestone: light accent background on heading area */
.milestone.is-active .body h3 { color: var(--accent-dark); }
.milestone.is-active .body {
  background: var(--accent-light);
  border-radius: 8px;
  padding: 10px 14px 36px;
  margin-left: -14px;
}

.milestone .tags { display: flex; gap: 8px; flex-wrap: wrap; }
.milestone .tag {
  font-family: var(--mono);
  font-size: 11.5px;
  background: var(--gray-150);
  border: 1px solid var(--gray-300);
  border-radius: 6px;
  padding: 3px 8px;
  color: var(--gray-700);
}
</style>

<div class="milestones">

  <div class="milestone">
    <div class="when">Week 1 · Mon–Tue</div>
    <div class="dot-col"><span class="dot done"></span><span class="line"></span></div>
    <div class="body">
      <h3>Schema &amp; API contract</h3>
      <p>Database migrations and router stubs. No UI. Contract reviewed before anything else lands.</p>
      <div class="tags"><span class="tag">packages/db</span><span class="tag">packages/api</span></div>
    </div>
  </div>

  <div class="milestone">
    <div class="when">Week 1 · Wed–Fri</div>
    <div class="dot-col"><span class="dot done"></span><span class="line"></span></div>
    <div class="body">
      <h3>Component &amp; composer</h3>
      <p>Static component rendered from fixtures. Optimistic insert on submit, rollback on failure.</p>
      <div class="tags"><span class="tag">apps/web</span><span class="tag">storybook</span></div>
    </div>
  </div>

  <div class="milestone is-active">
    <div class="when">Week 2 · Mon–Wed</div>
    <div class="dot-col"><span class="dot active"></span><span class="line"></span></div>
    <div class="body">
      <h3>Realtime &amp; unread state</h3>
      <p>Subscribe to the live channel. Track per-user read cursors for unread counts.</p>
      <div class="tags"><span class="tag">packages/realtime</span><span class="tag">apps/web</span></div>
    </div>
  </div>

  <div class="milestone">
    <div class="when">Week 2 · Thu–Fri</div>
    <div class="dot-col"><span class="dot"></span><span class="line"></span></div>
    <div class="body">
      <h3>Flag ramp &amp; docs</h3>
      <p>Ramp feature flag to internal, then 10% → 100% over three days.</p>
      <div class="tags"><span class="tag">packages/notify</span><span class="tag">growthbook</span></div>
    </div>
  </div>

</div>
```

---

## Component 2: Incident Timeline

**Name:** Incident Timeline

**Description:** A vertical timeline using a `::before` pseudo-element on the container for the connecting line. Each entry has an absolutely-positioned colored dot, a monospace timestamp pill, and a body text area. Three dot state variants: default gray (neutral event), `.impact` (accent — marks where user-visible impact begins), and `.mitigated` (olive — marks resolution). The last entry omits bottom padding. Inline `<code>` renders in a subtle light fill.

**When to use:** Incident postmortems, outage write-ups, deploy failure retrospectives, security event timelines. Also effective for documenting any process where the sequence of events and their timestamps are the primary content.

**Dark mode notes:** The `.tl-dot` uses `border: 2px solid #FAF9F5` (ivory) to cut a halo against the light page background. On a dark background, change this to match the dark surface color so the halo effect reads correctly. The timestamp pill's `background: #F0EEE6` stays warm and legible in light mode; swap to a darker surface token (e.g. `rgba(255,255,255,0.08)`) for dark contexts.

```html
<style>
:root {
  --ivory: #FAF9F5;
  --slate: #141413;
  --accent: #5B5FC7;
  --accent-light: #EEEEFF;
  --accent-dark: #3D4096;
  --accent-translucent: rgba(91,95,199,0.10);
  --olive: #788C5D;
  --rust: #B04A3F;
  --gray-100: #F0EEE6;
  --gray-300: #D1CFC5;
  --gray-500: #87867F;
  --gray-700: #3D3D3A;
  --white: #FFFFFF;
  --serif: ui-serif, Georgia, serif;
  --sans: system-ui, -apple-system, sans-serif;
  --mono: ui-monospace, 'SF Mono', Menlo, monospace;
}
* { margin: 0; padding: 0; box-sizing: border-box; }
body {
  font-family: var(--sans);
  background: var(--ivory);
  color: var(--gray-700);
  line-height: 1.6;
  padding: 32px;
  -webkit-font-smoothing: antialiased;
}

/* Vertical connector line runs along the left edge of the container */
.timeline {
  position: relative;
  padding: 4px 0 4px 16px;
  max-width: 640px;
}
.timeline::before {
  content: "";
  position: absolute;
  left: 16px;
  top: 8px;
  bottom: 8px;
  width: 2px;
  background: var(--gray-300);
}

/* Each entry is offset left to position the dot over the connector line */
.tl-entry {
  position: relative;
  padding: 0 0 22px 28px;
}
.tl-entry:last-child { padding-bottom: 0; }

/* Dot states */
.tl-dot {
  position: absolute;
  left: -5px;
  top: 6px;
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background: var(--gray-500);
  border: 2px solid var(--ivory);  /* halo separates dot from line */
  box-sizing: content-box;
}

/* Impact: accent color marks user-visible start of incident */
.tl-dot.impact {
  background: var(--accent);
  box-shadow: 0 0 0 2px var(--accent-translucent);
}

/* Mitigated: olive marks resolution */
.tl-dot.mitigated { background: var(--olive); }

/* Rust variant: for high-severity or data-loss events */
.tl-dot.critical { background: var(--rust); }

/* Timestamp pill */
.tl-time {
  display: inline-block;
  font-family: var(--mono);
  font-size: 12px;
  color: var(--gray-700);
  background: var(--gray-100);
  border: 1px solid var(--gray-300);
  border-radius: 6px;
  padding: 2px 8px;
  margin-bottom: 6px;
}

.tl-body {
  font-size: 14px;
  color: var(--gray-700);
  font-family: var(--sans);
}
.tl-body strong { color: var(--slate); font-weight: 600; }
.tl-body code {
  font-family: var(--mono);
  font-size: 13px;
  background: var(--gray-100);
  padding: 1.5px 5px;
  border-radius: 4px;
}

/* Impact banner: subtle accent background on the impact entry */
.tl-entry.is-impact { background: var(--accent-light); border-radius: 8px; padding: 8px 12px 14px 28px; margin-bottom: 8px; }
.tl-entry.is-impact .tl-body strong { color: var(--accent-dark); }
</style>

<div class="timeline">

  <div class="tl-entry">
    <span class="tl-dot"></span>
    <span class="tl-time">14:02</span>
    <div class="tl-body">
      Config change <code>cfg-9a12</code> promoted to production via the
      standard rollout pipeline.
    </div>
  </div>

  <div class="tl-entry is-impact">
    <span class="tl-dot impact"></span>
    <span class="tl-time">14:06</span>
    <div class="tl-body">
      <strong>Impact starts.</strong> Sync workers begin queueing on pool
      checkout; p95 latency climbs past 4s and the load balancer starts
      returning 502s.
    </div>
  </div>

  <div class="tl-entry">
    <span class="tl-dot"></span>
    <span class="tl-time">14:07</span>
    <div class="tl-body">
      Alert fires: <code>sync_5xx_rate &gt; 2%</code> for 60s. On-call acknowledges.
    </div>
  </div>

  <div class="tl-entry">
    <span class="tl-dot"></span>
    <span class="tl-time">14:18</span>
    <div class="tl-body">
      Initial hypothesis is a bad deploy; last two application deploys are
      rolled back with no effect.
    </div>
  </div>

  <div class="tl-entry">
    <span class="tl-dot mitigated"></span>
    <span class="tl-time">14:44</span>
    <div class="tl-body">
      <strong>Mitigated.</strong> <code>cfg-9a12</code> reverted; worker fleet
      cycled. 5xx rate drops below 0.2% within three minutes.
    </div>
  </div>

  <div class="tl-entry">
    <span class="tl-dot"></span>
    <span class="tl-time">14:49</span>
    <div class="tl-body">
      Monitors green for 5 minutes. Incident declared resolved.
    </div>
  </div>

</div>
```

**Dot state reference:**

| Class | Color | Use for |
|---|---|---|
| _(none)_ | `--gray-500` | Neutral event, observation, detection |
| `.impact` | `--accent` (`#5B5FC7`) | Start of user-visible impact |
| `.mitigated` | `--olive` (`#788C5D`) | Mitigation or resolution |
| `.critical` | `--rust` (`#B04A3F`) | Data loss, SEV-1, or cascading failures |

---

## Component 3: Sequence Timeline (Animation/Process Ruler)

**Name:** Sequence Timeline Ruler

**Description:** A horizontal ruler-style timeline showing named stages at fixed positions along a track. Each stage has a dot on the track, a label above the dot, and a timestamp below. The final stage dot uses `--olive` to visually mark the settled/complete state. Delay labels between dots sit on the track itself using absolutely-positioned spans. The whole component lives in a white bordered card with a serif heading, making it easy to drop inline in a post.

**When to use:** Documenting animation sequences, multi-step async pipelines, request lifecycles, deployment pipeline stages, or any process where relative timing between steps matters. Particularly useful in posts that explain how a sequenced interaction or multi-phase process was designed.

**Dark mode notes:** The track background is `--gray-200` (`#D1CFC5`) — readable on both light and dark panels. On a dark card, set `.timeline { background: #1F1E1B; border-color: #3D3D3A; }` and change `.key span` and `.key em` to use `#9C9A93` for muted text. The dot borders use `--white` for the halo; swap to the dark surface color.

```html
<style>
:root {
  --ivory: #FAF9F5;
  --slate: #141413;
  --accent: #5B5FC7;
  --accent-light: #EEEEFF;
  --accent-dark: #3D4096;
  --accent-translucent: rgba(91,95,199,0.10);
  --olive: #788C5D;
  --gray-200: #D1CFC5;
  --gray-500: #87867F;
  --gray-700: #3D3D3A;
  --white: #FFFFFF;
  --serif: ui-serif, Georgia, 'Times New Roman', serif;
  --sans: system-ui, -apple-system, 'Segoe UI', sans-serif;
  --mono: ui-monospace, 'SF Mono', Menlo, Consolas, monospace;
}
* { box-sizing: border-box; margin: 0; padding: 0; }
body {
  font-family: var(--sans);
  background: var(--ivory);
  font-size: 14px;
  padding: 32px;
  -webkit-font-smoothing: antialiased;
}

/* Card wrapper */
.seq-timeline {
  background: var(--white);
  border: 1.5px solid var(--gray-200);
  border-radius: 12px;
  padding: 22px 22px 48px;
  max-width: 600px;
}
.seq-timeline h2 {
  font-family: var(--serif);
  font-weight: 500;
  font-size: 16px;
  color: var(--slate);
  margin: 0 0 20px;
}

/* Horizontal track line */
.track {
  position: relative;
  height: 2px;
  background: var(--gray-200);
  margin: 24px 8px 0;
}

/* Keyframe dot: positioned along the track via left % */
.key {
  position: absolute;
  top: -5px;                        /* centers the 12px dot on the 2px track */
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background: var(--accent);
  border: 2px solid var(--white);   /* halo separates dot from track */
  box-shadow: 0 0 0 1.5px var(--accent);
  transform: translateX(-50%);
}

/* Final state dot uses olive (settled/complete) */
.key.final {
  background: var(--olive);
  box-shadow: 0 0 0 1.5px var(--olive);
}

/* Label above the dot */
.key em {
  position: absolute;
  bottom: 16px;
  left: 50%;
  transform: translateX(-50%);
  white-space: nowrap;
  font-style: normal;
  font-size: 11px;
  color: var(--gray-500);
  font-family: var(--sans);
}

/* Timestamp below the dot */
.key span {
  position: absolute;
  top: 16px;
  left: 50%;
  transform: translateX(-50%);
  white-space: nowrap;
  font-family: var(--mono);
  font-size: 10px;
  color: var(--gray-700);
}

/* Delay label between dots: centered on the track between two positions */
.delay {
  position: absolute;
  top: -8px;
  transform: translateX(-50%);
  font-family: var(--mono);
  font-size: 10px;
  color: var(--accent);
  background: var(--accent-light);
  border: 1px solid rgba(91,95,199,0.20);
  border-radius: 4px;
  padding: 1px 5px;
  white-space: nowrap;
}

/* Dot positions — adjust left % to match your timing ratios */
/* Example: 600ms total, positions are delay/600 * 100% */
.k0 { left: 0%;   }   /* 0ms   */
.k1 { left: 13%;  }   /* 80ms  */
.k2 { left: 20%;  }   /* 120ms */
.k3 { left: 33%;  }   /* 200ms */
.k4 { left: 100%; }   /* 600ms — final */

/* Delay labels centered between each pair */
.d01 { left: 6.5%;  }   /* midpoint of k0–k1 */
.d12 { left: 16.5%; }   /* midpoint of k1–k2 */
.d23 { left: 26.5%; }   /* midpoint of k2–k3 */
.d34 { left: 66.5%; }   /* midpoint of k3–k4 */
</style>

<div class="seq-timeline">
  <h2>Task completion — animation sequence</h2>
  <div class="track">

    <!-- Keyframe dots -->
    <div class="key k0"><em>fill</em><span>0ms</span></div>
    <div class="key k1"><em>check</em><span>80ms</span></div>
    <div class="key k2"><em>strike</em><span>120ms</span></div>
    <div class="key k3"><em>confetti</em><span>200ms</span></div>
    <div class="key final k4"><em>collapse</em><span>600ms</span></div>

    <!-- Delay labels between dots -->
    <span class="delay d01">+80ms</span>
    <span class="delay d12">+40ms</span>
    <span class="delay d23">+80ms</span>
    <span class="delay d34">+400ms</span>

  </div>
</div>
```

**Adapting positions:** Each `.key` uses `left: X%` where X is `delay_ms / total_ms * 100`. Delay labels use the midpoint between their flanking dots. To add or remove stages, add/remove `.key` and `.delay` divs and recalculate the percentages. The `final` class on the last dot visually marks the end state using `--olive`.

**Extending for multi-row sequences:** Duplicate the `.track` block inside `.seq-timeline` and add a row label to the left of each track to create a swim-lane style diagram showing parallel sequences (e.g. network request and UI animation running concurrently).
