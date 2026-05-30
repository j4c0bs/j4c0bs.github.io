# Interactive Components — HTML/CSS/JS Reference

Extracted and adapted from `07-prototype-animation.html` and `08-prototype-interaction.html`.

Accent color updated throughout: `--clay: #D97757` replaced with `--accent: #5B5FC7` (electric indigo).

All interactions are vanilla JS, self-contained within a scoped wrapper div.

---

## Shared Design Tokens

Include this `:root` block once per post when using any component below.

```css
:root {
  /* Core palette */
  --ivory:    #FAF9F5;
  --slate:    #141413;
  --oat:      #E3DACC;
  --olive:    #788C5D;
  --gray-50:  #F0EEE6;
  --gray-200: #D1CFC5;
  --gray-500: #87867F;
  --gray-800: #3D3D3A;
  --white:    #ffffff;

  /* Accent (electric indigo) */
  --accent:             #5B5FC7;
  --accent-light:       #EEEEFF;
  --accent-dark:        #3D4096;
  --accent-translucent: rgba(91,95,199,0.10);

  /* Typography */
  --serif: ui-serif, Georgia, "Times New Roman", serif;
  --sans:  system-ui, -apple-system, "Segoe UI", sans-serif;
  --mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;

  /* Default easing (spring overshoot ~18%) */
  --ease: cubic-bezier(0.34, 1.56, 0.64, 1);
}
```

---

## Easing Reference

| Name | Value | Character |
|------|-------|-----------|
| Spring (default) | `cubic-bezier(0.34, 1.56, 0.64, 1)` | Overshoots ~18%, snappy feel |
| Ease-out | `cubic-bezier(0.16, 1, 0.3, 1)` | Fast start, smooth deceleration |
| Linear | `linear` | No personality, useful for contrast |

---

## Component 1 — Task Completion Animation

**Name:** Task completion micro-interaction

**Description:** A task row that sequences through five distinct animation stages when clicked: circle fill with spring overshoot, SVG checkmark draw via `stroke-dashoffset`, label strikethrough, confetti burst, and row collapse. The entire sequence is driven by toggling a single `.done` class. All timing is controlled via CSS `transition-delay` — no JS timers. Click again to reset.

**When to use:** Illustrating stateful UI patterns, animation sequencing posts, task-management UX walkthroughs, or any post that benefits from a tangible interactive demo of CSS animation chains.

**Dark mode notes:** Swap `--ivory` → `#1A1A18`, `--white` → `#242422`, `--gray-200` → `#3A3A36`. The `--olive` settled state and `--accent` flash read clearly on dark backgrounds without adjustment.

```html
<div class="tc-demo">
<style>
  .tc-demo {
    --ivory:    #FAF9F5;
    --slate:    #141413;
    --accent:             #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91,95,199,0.10);
    --oat:      #E3DACC;
    --olive:    #788C5D;
    --gray-50:  #F0EEE6;
    --gray-200: #D1CFC5;
    --gray-500: #87867F;
    --gray-800: #3D3D3A;
    --white:    #ffffff;
    --sans:  system-ui, -apple-system, "Segoe UI", sans-serif;
    --mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;
    --ease: cubic-bezier(0.34, 1.56, 0.64, 1);

    background: var(--ivory);
    padding: 32px 16px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 12px;
    font-family: var(--sans);
    font-size: 14px;
    box-sizing: border-box;
  }
  .tc-demo *, .tc-demo *::before, .tc-demo *::after { box-sizing: border-box; }

  /* ---- task row ---- */
  .tc-task {
    display: flex;
    align-items: center;
    gap: 14px;
    padding: 14px 18px;
    border: 1.5px solid var(--gray-200);
    border-radius: 8px;
    background: var(--white);
    width: 360px;
    max-height: 64px;
    position: relative;
    /* collapse fires at 600ms after .done is added */
    transition: max-height 280ms var(--ease) 600ms,
                opacity    280ms var(--ease) 600ms,
                padding    280ms var(--ease) 600ms,
                border-color 200ms var(--ease);
    cursor: pointer;
    user-select: none;
  }
  .tc-task:hover { border-color: var(--gray-500); }

  /* ---- check circle ---- */
  .tc-check {
    flex: none;
    width: 24px;
    height: 24px;
    border-radius: 50%;
    border: 1.5px solid var(--gray-500);
    background: var(--white);
    position: relative;
    transition: background   180ms var(--ease),
                border-color 180ms var(--ease),
                transform    260ms var(--ease);
  }
  .tc-check svg {
    position: absolute;
    inset: 0;
    margin: auto;
    width: 14px;
    height: 14px;
  }
  /* checkmark drawn via stroke-dashoffset */
  .tc-check path {
    stroke: var(--white);
    stroke-width: 2.5;
    stroke-linecap: round;
    stroke-linejoin: round;
    fill: none;
    stroke-dasharray: 20;
    stroke-dashoffset: 20;
    transition: stroke-dashoffset 220ms var(--ease) 80ms;
  }

  /* ---- label & strikethrough ---- */
  .tc-label {
    position: relative;
    font-size: 15px;
    color: var(--slate);
    transition: color 200ms var(--ease) 120ms;
  }
  .tc-label::after {
    content: "";
    position: absolute;
    left: 0;
    top: 52%;
    height: 1.5px;
    width: 0;
    background: var(--gray-500);
    transition: width 240ms var(--ease) 120ms;
  }

  /* ---- due badge ---- */
  .tc-due {
    margin-left: auto;
    font-family: var(--mono);
    font-size: 11px;
    color: var(--gray-500);
    background: var(--gray-50);
    border: 1.5px solid var(--gray-200);
    padding: 3px 8px;
    border-radius: 8px;
    transition: opacity 200ms var(--ease) 120ms;
  }

  /* ---- confetti particles ---- */
  .tc-confetti {
    position: absolute;
    left: 22px;
    top: 50%;
    width: 6px;
    height: 6px;
    border-radius: 2px;
    opacity: 0;
    pointer-events: none;
  }
  .tc-confetti.c1 { background: var(--accent);      }
  .tc-confetti.c2 { background: var(--olive);        }
  .tc-confetti.c3 { background: var(--accent-light); }
  .tc-confetti.c4 { background: var(--accent-dark);  }
  .tc-confetti.c5 { background: var(--olive);        }
  .tc-confetti.c6 { background: var(--gray-800);     }

  /* =================== DONE STATE =================== */

  /* 1. Circle: accent flash → spring overshoot → settle at olive */
  .tc-task.done .tc-check {
    background: var(--olive);
    border-color: var(--olive);
    animation: tc-settle 380ms var(--ease) forwards;
  }
  @keyframes tc-settle {
    0%   { transform: scale(0.8);  background: var(--accent); border-color: var(--accent); }
    55%  { transform: scale(1.18); }
    100% { transform: scale(1);    background: var(--olive);  border-color: var(--olive);  }
  }

  /* 2. Checkmark draws at 80ms (transition-delay above) */
  .tc-task.done .tc-check path { stroke-dashoffset: 0; }

  /* 3. Label fades + strikethrough grows at 120ms */
  .tc-task.done .tc-label { color: var(--gray-500); }
  .tc-task.done .tc-label::after { width: 100%; }

  /* 4. Due badge fades */
  .tc-task.done .tc-due { opacity: 0.35; }

  /* 5. Row collapses at 600ms */
  .tc-task.done {
    max-height: 44px;
    padding-top: 8px;
    padding-bottom: 8px;
    opacity: 0.6;
  }

  /* 6. Confetti burst at 200ms */
  .tc-task.done .tc-confetti {
    animation: tc-pop 520ms var(--ease) 200ms forwards;
  }
  .tc-task.done .tc-confetti.c1 { --dx: -28px; --dy: -22px; --rot:  40deg; }
  .tc-task.done .tc-confetti.c2 { --dx:  -6px; --dy: -34px; --rot: -60deg; }
  .tc-task.done .tc-confetti.c3 { --dx:  18px; --dy: -26px; --rot:  90deg; }
  .tc-task.done .tc-confetti.c4 { --dx:  30px; --dy:  -4px; --rot: -30deg; }
  .tc-task.done .tc-confetti.c5 { --dx: -24px; --dy:  14px; --rot:  20deg; }
  .tc-task.done .tc-confetti.c6 { --dx:  12px; --dy:  22px; --rot: -80deg; }

  @keyframes tc-pop {
    0%   { opacity: 0; transform: translate(0, 0) rotate(0) scale(0.6); }
    15%  { opacity: 1; }
    100% { opacity: 0; transform: translate(var(--dx), var(--dy)) rotate(var(--rot)) scale(1); }
  }

  .tc-hint {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--gray-500);
  }
</style>

  <div class="tc-task" id="tcTask">
    <div class="tc-check">
      <svg viewBox="0 0 16 16">
        <path d="M3 8.5 L6.5 12 L13 4.5"/>
      </svg>
    </div>
    <span class="tc-label">Send weekly digest</span>
    <span class="tc-due">Fri</span>
    <div class="tc-confetti c1"></div>
    <div class="tc-confetti c2"></div>
    <div class="tc-confetti c3"></div>
    <div class="tc-confetti c4"></div>
    <div class="tc-confetti c5"></div>
    <div class="tc-confetti c6"></div>
  </div>
  <span class="tc-hint">click to toggle</span>

<script>
  (function () {
    var task = document.getElementById('tcTask');
    task.addEventListener('click', function () {
      task.classList.toggle('done');
    });
  })();
</script>
</div>
```

### Animation sequence summary

| Stage | Delay | Duration | Mechanism |
|-------|-------|----------|-----------|
| Circle fill (accent flash) | 0ms | 380ms | `@keyframes tc-settle` on `.tc-check` |
| Checkmark draw | 80ms | 220ms | `stroke-dashoffset` on `path` |
| Strikethrough | 120ms | 240ms | `width` on `::after` pseudo |
| Confetti burst | 200ms | 520ms | `@keyframes tc-pop` on `.tc-confetti` |
| Row collapse | 600ms | 280ms | `max-height`, `opacity`, `padding` |

---

## Component 2 — Checkbox Checklist

**Name:** Interactive checkbox checklist

**Description:** A done/pending checklist using fully custom CSS checkboxes — `appearance: none` removes all native browser styling, and a `::after` pseudo-element draws the checkmark using a rotated two-border rectangle. Checked state uses the accent color. Completed items show a strikethrough with a lighter `text-decoration-color` than the label text. Each item is clickable via a `<label>` wrapping an `<input type="checkbox">`.

**When to use:** Test plans, reading guides, action item lists, feature checklists in technical posts. Use when you want the interactive check behavior but more control over visual styling than `accent-color` allows.

**Dark mode notes:** Override `--white` → dark card color, `--gray-200` → darker border. The accent fill (`--accent`) on dark backgrounds may benefit from using `--accent-light` as the checkmark stroke color instead of white.

```html
<div class="cl-demo">
<style>
  .cl-demo {
    --slate:    #141413;
    --accent:             #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91,95,199,0.10);
    --olive:    #788C5D;
    --gray-50:  #F0EEE6;
    --gray-200: #D1CFC5;
    --gray-500: #87867F;
    --gray-800: #3D3D3A;
    --white:    #ffffff;
    --serif: ui-serif, Georgia, "Times New Roman", serif;
    --sans:  system-ui, -apple-system, "Segoe UI", sans-serif;
    --mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;

    font-family: var(--sans);
    font-size: 14px;
    padding: 24px 16px;
    box-sizing: border-box;
  }
  .cl-demo *, .cl-demo *::before, .cl-demo *::after { box-sizing: border-box; }

  .cl-panel {
    background: var(--white);
    border: 1.5px solid var(--gray-200);
    border-radius: 12px;
    padding: 20px 24px;
    max-width: 520px;
  }

  .cl-panel h3 {
    font-family: var(--serif);
    font-weight: 500;
    font-size: 18px;
    color: var(--slate);
    margin: 0 0 14px;
  }

  .cl-list {
    list-style: none;
    margin: 0;
    padding: 0;
    display: flex;
    flex-direction: column;
    gap: 2px;
  }

  .cl-list li {
    display: flex;
    align-items: flex-start;
    gap: 12px;
    padding: 10px 0;
    border-top: 1px solid var(--gray-50);
  }
  .cl-list li:first-child { border-top: none; }

  /* Custom checkbox — appearance:none strips all native styles */
  .cl-list input[type="checkbox"] {
    appearance: none;
    -webkit-appearance: none;
    width: 18px;
    height: 18px;
    border: 1.5px solid var(--gray-200);
    border-radius: 5px;
    background: var(--white);
    flex-shrink: 0;
    margin-top: 1px;
    cursor: pointer;
    position: relative;
    transition: background 150ms ease, border-color 150ms ease;
  }

  /* Checked state: accent fill */
  .cl-list input[type="checkbox"]:checked {
    background: var(--accent);
    border-color: var(--accent);
  }

  /* CSS checkmark via ::after */
  .cl-list input[type="checkbox"]:checked::after {
    content: "";
    position: absolute;
    left: 4px;
    top: 1px;
    width: 5px;
    height: 9px;
    border: solid var(--white);
    border-width: 0 2px 2px 0;
    transform: rotate(40deg);
  }

  /* Focus ring using accent */
  .cl-list input[type="checkbox"]:focus-visible {
    outline: 2px solid var(--accent);
    outline-offset: 2px;
  }

  /* Hover tint when unchecked */
  .cl-list input[type="checkbox"]:not(:checked):hover {
    border-color: var(--accent);
    background: var(--accent-translucent);
  }

  .cl-list label {
    cursor: pointer;
    flex: 1;
    font-size: 14px;
    color: var(--gray-800);
    line-height: 1.5;
    transition: color 150ms ease;
  }

  /* Strikethrough on done items — lighter decoration color than text */
  .cl-list input[type="checkbox"]:checked + label {
    color: var(--gray-500);
    text-decoration: line-through;
    text-decoration-color: var(--gray-200);
  }

  .cl-list code {
    font-family: var(--mono);
    font-size: 12.5px;
    background: var(--gray-50);
    padding: 1px 5px;
    border-radius: 4px;
  }
</style>

  <div class="cl-panel">
    <h3>Test plan</h3>
    <ul class="cl-list">
      <li>
        <input type="checkbox" id="cl1" checked>
        <label for="cl1">Unit: retry boundary, channel mute, singleton dedupe (14 cases)</label>
      </li>
      <li>
        <input type="checkbox" id="cl2" checked>
        <label for="cl2">Integration: create record with 3 watchers, assert 9 jobs enqueued and drained</label>
      </li>
      <li>
        <input type="checkbox" id="cl3">
        <label for="cl3">Manual: kill SMTP mid-burst, confirm jobs land in dead-letter</label>
      </li>
      <li>
        <input type="checkbox" id="cl4">
        <label for="cl4">Load: verify <code>p95</code> stays under 200ms at 2× baseline traffic</label>
      </li>
    </ul>
  </div>

</div>
```

---

## Component 3 — Drag-to-Reorder List

**Name:** Drag-to-reorder list

**Description:** A sidebar nav list with native HTML5 drag-and-drop reordering. No libraries. The dragged item becomes a ghost (35% opacity, 2° tilt) while staying in the DOM so the user retains spatial context. An accent-colored drop indicator line snaps to row midpoints rather than raw cursor Y, reducing jitter. A 2×3 CSS grid of dots provides the grip affordance; the entire row remains draggable.

**When to use:** Demonstrating drag-and-drop UX patterns, prioritization interfaces, list management posts. Also works as an embedded interactive example in posts about event handling or DOM manipulation.

**JS overview (~40 lines):** `dragstart` marks the ghost element; `dragover` iterates siblings to find the first item whose midpoint is below the cursor and positions the indicator; `drop` inserts the dragged node before the target (or appends if at the end); `dragend`/`dragleave` clean up state.

**Dark mode notes:** Set `--white` → `#242422`, `--gray-50` → `#2A2A28`. The accent drop indicator (`--accent`) is vivid enough on dark backgrounds without adjustment.

```html
<div class="dr-demo">
<style>
  .dr-demo {
    --ivory:    #FAF9F5;
    --slate:    #141413;
    --accent:             #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91,95,199,0.10);
    --gray-50:  #F0EEE6;
    --gray-200: #D1CFC5;
    --gray-500: #87867F;
    --gray-800: #3D3D3A;
    --white:    #ffffff;
    --sans:  system-ui, -apple-system, "Segoe UI", sans-serif;
    --mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;

    background: var(--ivory);
    padding: 32px 16px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 10px;
    font-family: var(--sans);
    font-size: 14px;
    box-sizing: border-box;
  }
  .dr-demo *, .dr-demo *::before, .dr-demo *::after { box-sizing: border-box; }

  /* ---- sidebar container ---- */
  .dr-sidebar {
    width: 260px;
    background: var(--white);
    border: 1.5px solid var(--gray-200);
    border-radius: 12px;
    padding: 14px 10px;
  }

  .dr-sidebar-title {
    font-family: var(--mono);
    font-size: 10px;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--gray-500);
    padding: 4px 10px 10px;
  }

  /* ---- list ---- */
  .dr-list {
    position: relative;
    list-style: none;
    margin: 0;
    padding: 0;
  }

  /* ---- draggable row ---- */
  .dr-item {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 9px 10px;
    margin: 2px 0;
    border-radius: 8px;
    background: var(--white);
    cursor: grab;
    user-select: none;
    transition: background 120ms linear,
                transform  120ms ease-out,
                opacity    120ms linear;
  }
  .dr-item:hover { background: var(--gray-50); }
  .dr-item:hover .dr-grip i { background: var(--gray-800); }

  /* ghost state: faded + tilt reads as "lifted" */
  .dr-item.dragging {
    opacity: 0.35;
    transform: rotate(2deg);
    background: var(--gray-50);
    cursor: grabbing;
  }

  .dr-item .dr-label { font-size: 14px; color: var(--slate); }
  .dr-item .dr-count {
    margin-left: auto;
    font-family: var(--mono);
    font-size: 11px;
    color: var(--gray-500);
  }

  /* ---- grip: 2×3 dot grid, pure CSS ---- */
  .dr-grip {
    flex: none;
    width: 10px;
    height: 16px;
    display: grid;
    grid-template-columns: repeat(2, 3px);
    grid-template-rows:    repeat(3, 3px);
    gap: 3px;
    align-content: center;
  }
  .dr-grip i {
    width: 3px;
    height: 3px;
    border-radius: 50%;
    background: var(--gray-200);
    transition: background 120ms linear;
  }

  /* ---- drop indicator line ---- */
  .dr-indicator {
    position: absolute;
    left: 6px;
    right: 6px;
    height: 2px;
    background: var(--accent);
    border-radius: 2px;
    pointer-events: none;
    display: none;
  }
  /* leading dot on the left end of the line */
  .dr-indicator::before {
    content: "";
    position: absolute;
    left: -5px;
    top: -3px;
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: var(--accent);
  }
  .dr-indicator.on { display: block; }

  .dr-footnote {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--gray-500);
  }
</style>

  <nav class="dr-sidebar">
    <div class="dr-sidebar-title">Views</div>
    <ul class="dr-list" id="drList">
      <li class="dr-item" draggable="true">
        <span class="dr-grip"><i></i><i></i><i></i><i></i><i></i><i></i></span>
        <span class="dr-label">Inbox</span>
        <span class="dr-count">14</span>
      </li>
      <li class="dr-item" draggable="true">
        <span class="dr-grip"><i></i><i></i><i></i><i></i><i></i><i></i></span>
        <span class="dr-label">Today</span>
        <span class="dr-count">3</span>
      </li>
      <li class="dr-item" draggable="true">
        <span class="dr-grip"><i></i><i></i><i></i><i></i><i></i><i></i></span>
        <span class="dr-label">Upcoming</span>
        <span class="dr-count">21</span>
      </li>
      <li class="dr-item" draggable="true">
        <span class="dr-grip"><i></i><i></i><i></i><i></i><i></i><i></i></span>
        <span class="dr-label">Projects</span>
        <span class="dr-count">8</span>
      </li>
      <li class="dr-item" draggable="true">
        <span class="dr-grip"><i></i><i></i><i></i><i></i><i></i><i></i></span>
        <span class="dr-label">Archive</span>
        <span class="dr-count"></span>
      </li>
      <li class="dr-item" draggable="true">
        <span class="dr-grip"><i></i><i></i><i></i><i></i><i></i><i></i></span>
        <span class="dr-label">Trash</span>
        <span class="dr-count"></span>
      </li>
      <!-- indicator lives inside .dr-list so top is relative to the list -->
      <div class="dr-indicator" id="drIndicator"></div>
    </ul>
  </nav>
  <p class="dr-footnote">Order persists in DOM only — refresh to reset.</p>

<script>
  (function () {
    var list      = document.getElementById('drList');
    var indicator = document.getElementById('drIndicator');
    var dragging  = null;
    var dropBefore = null; // node to insert before; null = append

    list.addEventListener('dragstart', function (e) {
      var item = e.target.closest('.dr-item');
      if (!item) return;
      dragging = item;
      item.classList.add('dragging');
      e.dataTransfer.effectAllowed = 'move';
      e.dataTransfer.setData('text/plain', ''); // required for Firefox
    });

    list.addEventListener('dragover', function (e) {
      if (!dragging) return;
      e.preventDefault();

      // collect only .dr-item children (excludes the indicator div)
      var siblings = Array.prototype.filter.call(
        list.children,
        function (el) { return el.classList && el.classList.contains('dr-item'); }
      );

      // find first item whose midpoint is below the cursor
      dropBefore = null;
      for (var i = 0; i < siblings.length; i++) {
        var r = siblings[i].getBoundingClientRect();
        if (e.clientY < r.top + r.height / 2) {
          dropBefore = siblings[i];
          break;
        }
      }

      // position indicator at the target gap
      var listRect = list.getBoundingClientRect();
      var edge = dropBefore
        ? dropBefore.getBoundingClientRect().top
        : siblings[siblings.length - 1].getBoundingClientRect().bottom;
      indicator.style.top = (edge - listRect.top - 1) + 'px';
      indicator.classList.add('on');
    });

    list.addEventListener('drop', function (e) {
      e.preventDefault();
      if (!dragging) return;
      if (dropBefore) list.insertBefore(dragging, dropBefore);
      else            list.insertBefore(dragging, indicator); // append before indicator
      cleanup();
    });

    list.addEventListener('dragleave', function (e) {
      // hide indicator only when cursor truly leaves the list
      if (!list.contains(e.relatedTarget)) indicator.classList.remove('on');
    });

    document.addEventListener('dragend', cleanup);

    function cleanup() {
      if (dragging) dragging.classList.remove('dragging');
      dragging   = null;
      dropBefore = null;
      indicator.classList.remove('on');
    }
  })();
</script>
</div>
```

### Drag interaction timings

| Property | Duration | Easing |
|----------|----------|--------|
| Item background on hover | 120ms | `linear` |
| Item transform (tilt on drag) | 120ms | `ease-out` |
| Item opacity (ghost) | 120ms | `linear` |
| Grip dot color on hover | 120ms | `linear` |

---

## Component 4 — Easing Switcher

**Name:** Easing switcher control panel

**Description:** A control panel with radio-style buttons that swap a single `--ease` CSS custom property on the wrapper div, so every transition in the component responds simultaneously. A preview box animates on hover to demonstrate the selected easing curve. Button active state uses the accent color. Useful as a design review tool embedded in animation posts, or as a live companion to easing curve explanations.

**When to use:** Animation and motion design posts, CSS transition deep-dives, design system documentation. Also useful as a standalone "feel the difference" interactive element for any post discussing timing.

**Dark mode notes:** Swap `--ivory` → dark background, `--white` → dark card. The active button's `--accent-translucent` background and `--accent` border remain visually distinct on dark surfaces.

```html
<div class="es-demo">
<style>
  .es-demo {
    --ivory:    #FAF9F5;
    --slate:    #141413;
    --accent:             #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91,95,199,0.10);
    --gray-50:  #F0EEE6;
    --gray-200: #D1CFC5;
    --gray-500: #87867F;
    --white:    #ffffff;
    --serif: ui-serif, Georgia, "Times New Roman", serif;
    --sans:  system-ui, -apple-system, "Segoe UI", sans-serif;
    --mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;
    --ease: cubic-bezier(0.34, 1.56, 0.64, 1); /* swapped by panel */

    background: var(--ivory);
    padding: 32px 16px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-family: var(--sans);
    font-size: 14px;
    box-sizing: border-box;
  }
  .es-demo *, .es-demo *::before, .es-demo *::after { box-sizing: border-box; }

  /* ---- preview box ---- */
  .es-box {
    width: 48px;
    height: 48px;
    background: var(--accent);
    border-radius: 8px;
    transition: transform 400ms var(--ease);
    margin: 0 auto 16px;
  }
  .es-box:hover { transform: scale(1.4); }

  /* ---- panel ---- */
  .es-panel {
    background: var(--white);
    border: 1.5px solid var(--gray-200);
    border-radius: 12px;
    padding: 18px;
    width: 240px;
  }

  .es-panel h2 {
    font-family: var(--serif);
    font-weight: 500;
    font-size: 16px;
    margin: 0 0 4px;
    color: var(--slate);
  }

  .es-panel p {
    margin: 0 0 14px;
    font-size: 12px;
    color: var(--gray-500);
  }

  /* ---- easing buttons ---- */
  .es-btn {
    display: block;
    width: 100%;
    text-align: left;
    background: var(--gray-50);
    border: 1.5px solid var(--gray-200);
    border-radius: 8px;
    padding: 10px 12px;
    margin-bottom: 8px;
    font-family: var(--sans);
    font-size: 13px;
    color: var(--slate);
    cursor: pointer;
    transition: border-color 150ms ease, background 150ms ease;
  }
  .es-btn:last-child { margin-bottom: 0; }
  .es-btn:hover { border-color: var(--gray-500); }

  /* active state uses accent */
  .es-btn.active {
    border-color: var(--accent);
    background: var(--accent-translucent);
  }

  .es-btn code {
    display: block;
    font-family: var(--mono);
    font-size: 10px;
    color: var(--gray-500);
    margin-top: 2px;
  }
</style>

  <div class="es-panel">
    <div class="es-box"></div>
    <h2>Easing</h2>
    <p>Hover the box above to feel the difference. The active easing applies globally via <code style="font-family:monospace;font-size:11px">--ease</code>.</p>

    <button class="es-btn" data-ease="linear">
      Linear
      <code>linear</code>
    </button>
    <button class="es-btn" data-ease="cubic-bezier(0.16, 1, 0.3, 1)">
      Ease-out
      <code>cubic-bezier(.16, 1, .3, 1)</code>
    </button>
    <button class="es-btn active" data-ease="cubic-bezier(0.34, 1.56, 0.64, 1)">
      Spring
      <code>cubic-bezier(.34, 1.56, .64, 1)</code>
    </button>
  </div>

<script>
  (function () {
    var wrapper = document.querySelector('.es-demo');
    var buttons = wrapper.querySelectorAll('.es-btn');
    buttons.forEach(function (btn) {
      btn.addEventListener('click', function () {
        buttons.forEach(function (b) { b.classList.remove('active'); });
        btn.classList.add('active');
        // swap --ease on the scoped wrapper, not :root
        wrapper.style.setProperty('--ease', btn.dataset.ease);
      });
    });
  })();
</script>
</div>
```

---

## CSS Quick Reference

### Stroke-dashoffset checkmark draw

```css
.check path {
  stroke-dasharray: 20;      /* total path length */
  stroke-dashoffset: 20;     /* starts fully hidden */
  transition: stroke-dashoffset 220ms cubic-bezier(0.34, 1.56, 0.64, 1) 80ms;
}
.done .check path {
  stroke-dashoffset: 0;      /* draws the path */
}
```

### CSS-only confetti with per-particle custom properties

```css
@keyframes pop {
  0%   { opacity: 0; transform: translate(0, 0) rotate(0) scale(0.6); }
  15%  { opacity: 1; }
  100% { opacity: 0; transform: translate(var(--dx), var(--dy)) rotate(var(--rot)) scale(1); }
}

/* set per-particle vectors in the .done ruleset */
.done .c1 { --dx: -28px; --dy: -22px; --rot:  40deg; }
.done .c2 { --dx:  -6px; --dy: -34px; --rot: -60deg; }
/* etc. */
```

### 2×3 drag grip dots

```css
.grip {
  width: 10px;
  height: 16px;
  display: grid;
  grid-template-columns: repeat(2, 3px);
  grid-template-rows: repeat(3, 3px);
  gap: 3px;
  align-content: center;
}
.grip i {
  width: 3px;
  height: 3px;
  border-radius: 50%;
  background: var(--gray-200);
  transition: background 120ms linear;
}
.item:hover .grip i { background: var(--gray-800); }
```

### Drop indicator line with leading dot

```css
.indicator {
  position: absolute;   /* relative to .list */
  left: 6px;
  right: 6px;
  height: 2px;
  background: var(--accent);   /* #5B5FC7 */
  border-radius: 2px;
  pointer-events: none;
  display: none;
}
.indicator::before {
  content: "";
  position: absolute;
  left: -5px;
  top: -3px;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: var(--accent);
}
.indicator.on { display: block; }
```

### Custom checkbox (appearance:none + pseudo checkmark)

```css
input[type="checkbox"] {
  appearance: none;
  -webkit-appearance: none;
  width: 18px;
  height: 18px;
  border: 1.5px solid var(--gray-200);
  border-radius: 5px;
  background: var(--white);
  position: relative;
  transition: background 150ms ease, border-color 150ms ease;
}
input[type="checkbox"]:checked {
  background: var(--accent);
  border-color: var(--accent);
}
input[type="checkbox"]:checked::after {
  content: "";
  position: absolute;
  left: 4px;
  top: 1px;
  width: 5px;
  height: 9px;
  border: solid var(--white);
  border-width: 0 2px 2px 0;   /* right and bottom borders only */
  transform: rotate(40deg);     /* rotate into checkmark shape */
}
```

### Spring overshoot keyframe

```css
@keyframes settle {
  0%   { transform: scale(0.8);  background: var(--accent); border-color: var(--accent); }
  55%  { transform: scale(1.18); }
  100% { transform: scale(1);    background: var(--olive);  border-color: var(--olive);  }
}
.done .check {
  animation: settle 380ms cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
}
```

### Staggered collapse via transition-delay

```css
.task {
  max-height: 64px;
  opacity: 1;
  transition: max-height 280ms var(--ease) 600ms,
              opacity    280ms var(--ease) 600ms,
              padding    280ms var(--ease) 600ms;
}
.task.done {
  max-height: 44px;
  padding-top: 8px;
  padding-bottom: 8px;
  opacity: 0.6;
  /* all three fire at 600ms — after the celebration completes */
}
```

### Strikethrough with separate decoration color

```css
.done-label {
  color: var(--gray-500);
  text-decoration: line-through;
  text-decoration-color: var(--gray-200);  /* lighter than text color */
}
```
