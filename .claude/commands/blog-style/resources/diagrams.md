# Diagrams & Illustrations

SVG-based visual elements for blog posts. All components are self-contained inline HTML+CSS+SVG with no external dependencies.

---

## Design Tokens (Diagrams)

These tokens extend the base palette. The `--accent` family replaces `--clay` in all diagram/illustration contexts.

```css
:root {
  /* Base palette */
  --ivory:    #FAF9F5;
  --slate:    #141413;
  --oat:      #E3DACC;
  --olive:    #788C5D;
  --rust:     #B04A3F;
  --gray-150: #F0EEE6;
  --gray-300: #D1CFC5;
  --gray-500: #87867F;
  --gray-700: #3D3D3A;

  /* Accent — electric indigo (replaces clay in diagrams) */
  --accent:             #5B5FC7;
  --accent-light:       #EEEEFF;
  --accent-dark:        #3D4096;
  --accent-translucent: rgba(91,95,199,0.10);

  /* Typography */
  --serif: ui-serif, Georgia, "Times New Roman", serif;
  --sans:  system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  --mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;
}
```

**SVG stroke conventions:**
- `stroke-width: 1.5` — neutral/secondary boxes and lines
- `stroke-width: 2` — emphasized containers (highlighted node, success/failure nodes)
- `rx="8"` on process rectangles (architecture/flowchart); `rx="10"` on illustration rectangles
- No drop shadows, no gradients

**Typography inside SVG:**
- Box labels: 11–12px monospace
- Annotations outside boxes: 12px sans-serif, `#87867F`
- Subtitles below main label: 10px monospace, `#87867F`

---

## 1. Figure Container with Caption

**Name:** `figure.canvas` with `figcaption`

**Description:** Wraps an SVG illustration in a rounded bordered card (12px radius). The caption area uses flexbox to left-align a title/subtitle pair and optionally right-align an action button (e.g., download).

**When to use:** Any time an SVG illustration or diagram needs a title, description, or download affordance. Pairs with all illustration SVGs below.

```html
<style>
  :root {
    --ivory:    #FAF9F5;
    --slate:    #141413;
    --oat:      #E3DACC;
    --gray-150: #F0EEE6;
    --gray-300: #D1CFC5;
    --gray-500: #87867F;
    --gray-700: #3D3D3A;
    --serif: ui-serif, Georgia, "Times New Roman", serif;
    --sans:  system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
    --mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;
  }

  figure {
    margin: 0 0 72px 0;
  }

  .canvas {
    border: 1px solid var(--gray-300);
    border-radius: 12px;
    overflow: hidden;
    background: var(--ivory);
  }

  .canvas svg {
    display: block;
    width: 100%;
    height: auto;
  }

  figcaption {
    display: flex;
    align-items: baseline;
    justify-content: space-between;
    gap: 24px;
    padding: 18px 4px 0;
  }

  .cap-text { flex: 1; }

  .cap-title {
    font-family: var(--serif);
    font-size: 19px;
    font-weight: 500;
    margin-bottom: 4px;
  }

  .cap-sub {
    font-size: 13px;
    line-height: 1.55;
    color: var(--gray-500);
  }

  .dl {
    flex-shrink: 0;
    font-family: var(--mono);
    font-size: 11px;
    color: var(--slate);
    background: var(--gray-150);
    border: 1px solid var(--gray-300);
    border-radius: 6px;
    padding: 7px 12px;
    cursor: pointer;
  }
  .dl:hover  { background: var(--oat); }
  .dl:active { transform: translateY(1px); }
</style>

<figure>
  <div class="canvas">
    <!-- SVG goes here -->
    <svg xmlns="http://www.w3.org/2000/svg" width="720" height="200" viewBox="0 0 720 200">
      <rect width="720" height="200" fill="#FAF9F5"/>
      <text x="360" y="108" text-anchor="middle"
            font-family="ui-monospace,'SF Mono',Menlo,Consolas,monospace"
            font-size="12" fill="#87867F">SVG content here</text>
    </svg>
  </div>
  <figcaption>
    <div class="cap-text">
      <div class="cap-title">Illustration Title</div>
      <div class="cap-sub">One-line description of what this illustrates.</div>
    </div>
    <button class="dl" data-target="my-svg-id" data-filename="output.svg">Download SVG</button>
  </figcaption>
</figure>

<script>
  document.querySelectorAll('.dl').forEach(function(btn) {
    btn.addEventListener('click', function() {
      var svg  = document.getElementById(btn.dataset.target);
      if (!svg) return;
      var src  = new XMLSerializer().serializeToString(svg);
      var blob = new Blob([src], { type: 'image/svg+xml;charset=utf-8' });
      var url  = URL.createObjectURL(blob);
      var a    = document.createElement('a');
      a.href = url;
      a.download = btn.dataset.filename || 'illustration.svg';
      a.click();
      setTimeout(function() { URL.revokeObjectURL(url); }, 1000);
    });
  });
</script>
```

---

## 2. SVG Architecture Diagram

**Name:** Architecture flow diagram with boxes and arrows

**Description:** Horizontal flow diagram with labeled rectangular nodes connected by directional arrows. A "hot" (accent) box variant highlights a specific node of interest — useful for pointing out where a change lives or which component is under discussion. Uses a single arrowhead marker definition.

**When to use:** System architecture overviews, request-flow diagrams, data pipeline layouts, code walkthrough diagrams showing which module is in focus.

```html
<style>
  :root {
    --accent:             #5B5FC7;
    --accent-translucent: rgba(91,95,199,0.10);
    --gray-300: #D1CFC5;
    --gray-500: #87867F;
    --mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;
  }

  .diagram-panel {
    border: 1.5px solid var(--gray-300);
    border-radius: 12px;
    background: #fff;
    padding: 20px;
    overflow-x: auto;
  }

  svg.flow { display: block; max-width: 100%; }
</style>

<div class="diagram-panel">
  <svg class="flow" viewBox="0 0 720 280" width="720" height="280"
       role="img" aria-label="Architecture flow diagram">
    <defs>
      <style>
        .m { font-family: ui-monospace, "SF Mono", Menlo, Consolas, monospace; font-size: 12px; }
        .s { font-family: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif; font-size: 12px; }
      </style>
      <!-- Arrowhead marker -->
      <marker id="arrowHead" viewBox="0 0 10 10" refX="9" refY="5"
              markerWidth="7" markerHeight="7" orient="auto-start-reverse">
        <path d="M 0 0 L 10 5 L 0 10 z" fill="#87867F"/>
      </marker>
    </defs>

    <!-- background -->
    <rect width="720" height="280" fill="#FAF9F5"/>

    <!-- Box: Client (standard) -->
    <g>
      <rect x="30" y="40" width="150" height="64" rx="8"
            fill="#fff" stroke="#D1CFC5" stroke-width="1.5"/>
      <text x="105" y="68" text-anchor="middle" class="m" fill="#141413">Client</text>
      <text x="105" y="84" text-anchor="middle" class="m" fill="#87867F" font-size="10">browser / SDK</text>
    </g>

    <!-- Box: API Route (standard) -->
    <g>
      <rect x="245" y="40" width="150" height="64" rx="8"
            fill="#fff" stroke="#D1CFC5" stroke-width="1.5"/>
      <text x="320" y="68" text-anchor="middle" class="m" fill="#141413">/api/handler</text>
      <text x="320" y="84" text-anchor="middle" class="m" fill="#87867F" font-size="10">route handler</text>
    </g>

    <!-- Box: Middleware (hot / accent-highlighted) -->
    <g>
      <rect x="460" y="40" width="180" height="64" rx="8"
            fill="rgba(91,95,199,0.10)" stroke="#5B5FC7" stroke-width="1.5"/>
      <text x="550" y="68" text-anchor="middle" class="m" fill="#141413">processRequest()</text>
      <text x="550" y="84" text-anchor="middle" class="m" fill="#87867F" font-size="10">middleware/core.ts</text>
    </g>

    <!-- Box: Store (standard, lower row) -->
    <g>
      <rect x="460" y="170" width="180" height="64" rx="8"
            fill="#fff" stroke="#D1CFC5" stroke-width="1.5"/>
      <text x="550" y="198" text-anchor="middle" class="m" fill="#141413">DataStore</text>
      <text x="550" y="214" text-anchor="middle" class="m" fill="#87867F" font-size="10">lib/store.ts</text>
    </g>

    <!-- Box: DB (standard, lower row) -->
    <g>
      <rect x="245" y="170" width="150" height="64" rx="8"
            fill="#fff" stroke="#D1CFC5" stroke-width="1.5"/>
      <text x="320" y="198" text-anchor="middle" class="m" fill="#141413">Postgres</text>
      <text x="320" y="214" text-anchor="middle" class="m" fill="#87867F" font-size="10">records table</text>
    </g>

    <!-- Arrows -->
    <line x1="180" y1="72" x2="244" y2="72"
          stroke="#87867F" stroke-width="1.5" fill="none"
          marker-end="url(#arrowHead)"/>
    <line x1="395" y1="72" x2="459" y2="72"
          stroke="#87867F" stroke-width="1.5" fill="none"
          marker-end="url(#arrowHead)"/>
    <line x1="550" y1="104" x2="550" y2="169"
          stroke="#87867F" stroke-width="1.5" fill="none"
          marker-end="url(#arrowHead)"/>
    <line x1="459" y1="202" x2="396" y2="202"
          stroke="#87867F" stroke-width="1.5" fill="none"
          marker-end="url(#arrowHead)"/>

    <!-- Edge labels -->
    <text x="212" y="62" text-anchor="middle" class="m" fill="#87867F" font-size="10">request</text>
    <text x="564" y="142" text-anchor="start"  class="m" fill="#87867F" font-size="10">lookup</text>
    <text x="427" y="194" text-anchor="end"    class="m" fill="#87867F" font-size="10">rows</text>
  </svg>
</div>
```

**Hot box pattern** — to highlight a node, give its `<rect>` this treatment:
```html
<!-- accent fill + accent border, stroke-width: 1.5 standard / 2 for extra emphasis -->
<rect ... fill="rgba(91,95,199,0.10)" stroke="#5B5FC7" stroke-width="1.5"/>
```

---

## 3. Flowchart with Interactive Nodes

**Name:** Interactive SVG flowchart with side panel

**Description:** Vertical flowchart with process nodes, diamond decision gates, and terminal nodes. Three arrowhead colors (gray / accent / olive). Clicking a node populates a sticky side panel with details. Layout uses CSS Grid: `minmax(0,1fr) 300px`, collapsing to single column below 920px.

**Node types:**
- `.node rect` — process step: white fill, gray-300 border, `rx=8`
- `.node.term rect` — terminal: gray-150 fill
- `.node.gate path` — decision: `<path>` diamond (four-point polygon), white fill, gray-300 border
- `.node.ok rect` — success terminal: olive tinted fill, olive border
- `.node.bad rect` — failure node: rust tinted fill, rust border
- `.node.active rect/path` — selected state: accent stroke at 2px

**Edge types:**
- `.edge` — default: gray-500 stroke, gray arrowhead
- `.edge.yes` — success path: olive stroke, olive arrowhead
- `.edge.no` — failure path: rust stroke, dashed `stroke-dasharray: 4 4`, rust arrowhead

**When to use:** Multi-step processes with branching logic, deploy pipeline diagrams, decision trees, any workflow where readers benefit from clicking into each step's details.

```html
<style>
  :root {
    --ivory:              #FAF9F5;
    --slate:              #141413;
    --accent:             #5B5FC7;
    --accent-light:       #EEEEFF;
    --accent-dark:        #3D4096;
    --accent-translucent: rgba(91,95,199,0.10);
    --oat:                #E3DACC;
    --olive:              #788C5D;
    --rust:               #B04A3F;
    --gray-150:           #F0EEE6;
    --gray-300:           #D1CFC5;
    --gray-500:           #87867F;
    --gray-700:           #3D3D3A;
    --serif: ui-serif, Georgia, "Times New Roman", serif;
    --sans:  system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
    --mono:  ui-monospace, "SF Mono", Menlo, Consolas, monospace;
  }

  .layout {
    display: grid;
    grid-template-columns: minmax(0, 1fr) 300px;
    gap: 32px;
    align-items: start;
  }
  @media (max-width: 920px) { .layout { grid-template-columns: 1fr; } }

  .canvas {
    border: 1.5px solid var(--gray-300);
    border-radius: 14px;
    background: #fff;
    padding: 28px;
    overflow-x: auto;
  }
  svg.flow { display: block; width: 100%; height: auto; }

  /* SVG text defaults */
  .flow text  { font-family: var(--mono); font-size: 12px; fill: var(--slate); pointer-events: none; }
  .flow .sub  { font-size: 10px; fill: var(--gray-500); }
  .flow .lbl  { font-size: 10px; fill: var(--gray-500); }

  /* Node base styles */
  .node rect       { fill: #fff; stroke: var(--gray-300); stroke-width: 1.5; }
  .node.term rect  { fill: var(--gray-150); }
  .node.gate path  { fill: #fff; stroke: var(--gray-300); stroke-width: 1.5; }
  .node.ok rect    { fill: rgba(120,140,93,0.12);  stroke: var(--olive); }
  .node.bad rect   { fill: rgba(176,74,63,0.10);   stroke: var(--rust);  }

  /* Node interaction */
  .node            { cursor: pointer; transition: transform 120ms ease; }
  .node:hover      { transform: translateY(-1px); }
  .node.active rect,
  .node.active path { stroke: var(--accent); stroke-width: 2; }

  /* Edge styles */
  .edge     { stroke: var(--gray-500); stroke-width: 1.5; fill: none; marker-end: url(#arrow); }
  .edge.no  { stroke: var(--rust);     stroke-dasharray: 4 4; marker-end: url(#arrow-rust); }
  .edge.yes { stroke: var(--olive);    marker-end: url(#arrow-olive); }

  /* Side panel */
  aside {
    position: sticky;
    top: 24px;
    border: 1.5px solid var(--gray-300);
    border-radius: 14px;
    background: #fff;
    padding: 20px 20px 22px;
  }
  aside .hint { font-size: 12px; color: var(--gray-500); margin-bottom: 14px; }
  aside h3    { font-family: var(--serif); font-weight: 500; font-size: 19px; margin-bottom: 6px; }
  aside .meta { font-family: var(--mono); font-size: 11px; color: var(--gray-500); margin-bottom: 14px; }
  aside p     { font-size: 13.5px; line-height: 1.6; color: var(--gray-700); margin-bottom: 12px; }
  aside pre   {
    font-family: var(--mono);
    font-size: 11.5px;
    line-height: 1.55;
    background: var(--gray-150);
    border: 1px solid var(--gray-300);
    border-radius: 8px;
    padding: 10px 12px;
    white-space: pre-wrap;
    color: var(--slate);
  }

  /* Legend */
  .legend      { display: flex; flex-wrap: wrap; gap: 18px; margin-top: 18px; font-size: 12px; color: var(--gray-700); }
  .legend span { display: inline-flex; align-items: center; gap: 8px; }
  .chip        { width: 22px; height: 14px; border: 1.5px solid var(--gray-300); border-radius: 4px; background: #fff; }
  .chip.gate   { transform: rotate(45deg); width: 12px; height: 12px; border-radius: 2px; }
  .chip.ok     { background: rgba(120,140,93,0.12); border-color: var(--olive); }
  .chip.bad    { background: rgba(176,74,63,0.10);  border-color: var(--rust);  }
</style>

<div class="layout">
  <div>
    <div class="canvas">
      <svg class="flow" viewBox="0 0 620 720">
        <defs>
          <!-- Three-color arrowhead set -->
          <marker id="arrow" viewBox="0 0 10 10" refX="9" refY="5"
                  markerWidth="6" markerHeight="6" orient="auto-start-reverse">
            <path d="M0,0 L10,5 L0,10 z" fill="#87867F"/>
          </marker>
          <marker id="arrow-rust" viewBox="0 0 10 10" refX="9" refY="5"
                  markerWidth="6" markerHeight="6" orient="auto-start-reverse">
            <path d="M0,0 L10,5 L0,10 z" fill="#B04A3F"/>
          </marker>
          <marker id="arrow-olive" viewBox="0 0 10 10" refX="9" refY="5"
                  markerWidth="6" markerHeight="6" orient="auto-start-reverse">
            <path d="M0,0 L10,5 L0,10 z" fill="#788C5D"/>
          </marker>
        </defs>

        <!-- Edges — draw before nodes so nodes render on top -->
        <path class="edge"     d="M310,56 L310,92"/>
        <path class="edge"     d="M310,140 L310,176"/>
        <path class="edge"     d="M310,224 L310,262"/>
        <path class="edge yes" d="M310,326 L310,368"/>
        <text class="lbl" x="320" y="350">pass</text>
        <path class="edge no"  d="M268,294 C190,294 150,294 150,212 L150,212"/>
        <text class="lbl" x="138" y="260" fill="#B04A3F">fail</text>
        <path class="edge"     d="M310,416 L310,452"/>
        <path class="edge yes" d="M310,500 L310,538"/>
        <text class="lbl" x="320" y="522">ok</text>
        <path class="edge no"  d="M352,476 C450,476 450,560 410,580"/>
        <text class="lbl" x="428" y="520" fill="#B04A3F">fail</text>
        <path class="edge"     d="M310,580 L310,618"/>

        <!-- Node: Start terminal -->
        <g class="node term active" data-k="start">
          <rect x="230" y="12" width="160" height="44" rx="8"/>
          <text x="310" y="38" text-anchor="middle">start</text>
        </g>

        <!-- Node: Step A -->
        <g class="node" data-k="step-a">
          <rect x="210" y="92" width="200" height="48" rx="8"/>
          <text x="310" y="112" text-anchor="middle">Step A</text>
          <text class="sub" x="310" y="128" text-anchor="middle">description · duration</text>
        </g>

        <!-- Node: Step B -->
        <g class="node" data-k="step-b">
          <rect x="210" y="176" width="200" height="48" rx="8"/>
          <text x="310" y="196" text-anchor="middle">Step B</text>
          <text class="sub" x="310" y="212" text-anchor="middle">description · duration</text>
        </g>

        <!-- Node: Decision gate -->
        <!-- Diamond center=(310,294), half-width=42, half-height=32 -->
        <g class="node gate" data-k="gate-1">
          <path d="M310,262 L352,294 L310,326 L268,294 Z"/>
          <text x="310" y="298" text-anchor="middle">check?</text>
        </g>

        <!-- Node: Failure side path -->
        <g class="node bad" data-k="on-fail">
          <rect x="60" y="176" width="170" height="48" rx="8"/>
          <text x="145" y="196" text-anchor="middle">Handle Failure</text>
          <text class="sub" x="145" y="212" text-anchor="middle">alert / log</text>
        </g>

        <!-- Node: Step C -->
        <g class="node" data-k="step-c">
          <rect x="210" y="368" width="200" height="48" rx="8"/>
          <text x="310" y="388" text-anchor="middle">Step C</text>
          <text class="sub" x="310" y="404" text-anchor="middle">description · duration</text>
        </g>

        <!-- Node: Step D with inner decision -->
        <g class="node" data-k="step-d">
          <rect x="210" y="452" width="200" height="48" rx="8"/>
          <text x="310" y="472" text-anchor="middle">Step D</text>
          <text class="sub" x="310" y="488" text-anchor="middle">soak / validation</text>
        </g>

        <!-- Node: Rollback (failure) -->
        <g class="node bad" data-k="rollback">
          <rect x="380" y="560" width="160" height="44" rx="8"/>
          <text x="460" y="586" text-anchor="middle">Rollback</text>
        </g>

        <!-- Node: Done terminal (success) -->
        <g class="node ok term" data-k="done">
          <rect x="230" y="618" width="160" height="44" rx="8"/>
          <text x="310" y="644" text-anchor="middle">complete</text>
        </g>
      </svg>
    </div>

    <div class="legend">
      <span><i class="chip"></i> process step</span>
      <span><i class="chip gate"></i> decision</span>
      <span><i class="chip ok"></i> terminal success</span>
      <span><i class="chip bad"></i> failure path</span>
    </div>
  </div>

  <aside id="panel">
    <div class="hint">Click a step in the chart</div>
    <h3 id="p-title">start</h3>
    <div class="meta" id="p-meta">trigger · 0s</div>
    <p id="p-body">Entry point for the process.</p>
    <pre id="p-code">// detail snippet here</pre>
  </aside>
</div>

<script>
const DETAIL = {
  "start":  { title: "start",        meta: "trigger",           body: "Entry point.",               code: "// trigger condition" },
  "step-a": { title: "Step A",       meta: "phase 1 · ~N min",  body: "What this step does.",       code: "// step A code" },
  "step-b": { title: "Step B",       meta: "phase 2 · ~N min",  body: "What this step does.",       code: "// step B code" },
  "gate-1": { title: "Check?",       meta: "decision",          body: "What condition is tested.",  code: "// condition" },
  "on-fail":{ title: "Handle Fail",  meta: "side-effect",       body: "Failure handling logic.",    code: "// failure handler" },
  "step-c": { title: "Step C",       meta: "phase 3 · ~N min",  body: "What this step does.",       code: "// step C code" },
  "step-d": { title: "Step D",       meta: "phase 4 · ~N min",  body: "Validation / soak period.",  code: "// step D code" },
  "rollback":{ title: "Rollback",    meta: "recovery · ~30s",   body: "Reverts to previous state.", code: "// rollback logic" },
  "done":   { title: "complete",     meta: "terminal",          body: "Process finished.",          code: "// done" }
};

const nodes = document.querySelectorAll(".node");
const T = document.getElementById("p-title");
const M = document.getElementById("p-meta");
const B = document.getElementById("p-body");
const C = document.getElementById("p-code");

nodes.forEach(function(n) {
  n.addEventListener("click", function() {
    nodes.forEach(function(x) { x.classList.remove("active"); });
    n.classList.add("active");
    var d = DETAIL[n.dataset.k];
    if (!d) return;
    T.textContent = d.title;
    M.textContent = d.meta;
    B.textContent = d.body;
    C.textContent = d.code;
  });
});
</script>
```

---

## 4. SVG Illustrations

Three structural patterns for common data-engineering concepts. All share the same token set and SVG conventions. Wrap each in a `figure.canvas` (see element 1) for captions.

### 4a. Queue (FIFO)

**Description:** Five job boxes in a horizontal row. The head-of-queue box uses accent fill (highlighted). An arrow points to a worker box on the right. Demonstrates `rx=10`, flat fills, inline marker, dual-font text.

```html
<svg id="svg-queue" xmlns="http://www.w3.org/2000/svg"
     width="720" height="320" viewBox="0 0 720 320">
  <defs>
    <style>
      .m { font-family: ui-monospace, "SF Mono", Menlo, Consolas, monospace; font-size: 11px; }
      .s { font-family: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif; font-size: 12px; }
    </style>
    <marker id="qa" markerWidth="10" markerHeight="10" refX="8" refY="4"
            orient="auto" markerUnits="userSpaceOnUse">
      <path d="M0,0 L0,8 L8,4 z" fill="#87867F"/>
    </marker>
  </defs>

  <!-- background -->
  <rect width="720" height="320" fill="#FAF9F5"/>

  <!-- queue label -->
  <text x="60" y="96" class="s" fill="#87867F">queue</text>

  <!-- jobs 5–2 (standard) -->
  <rect x="60"  y="110" width="70" height="100" rx="10" fill="#F0EEE6" stroke="#3D3D3A" stroke-width="1.5"/>
  <text x="95"  y="164" text-anchor="middle" class="m" fill="#3D3D3A">job 5</text>

  <rect x="140" y="110" width="70" height="100" rx="10" fill="#F0EEE6" stroke="#3D3D3A" stroke-width="1.5"/>
  <text x="175" y="164" text-anchor="middle" class="m" fill="#3D3D3A">job 4</text>

  <rect x="220" y="110" width="70" height="100" rx="10" fill="#F0EEE6" stroke="#3D3D3A" stroke-width="1.5"/>
  <text x="255" y="164" text-anchor="middle" class="m" fill="#3D3D3A">job 3</text>

  <rect x="300" y="110" width="70" height="100" rx="10" fill="#F0EEE6" stroke="#3D3D3A" stroke-width="1.5"/>
  <text x="335" y="164" text-anchor="middle" class="m" fill="#3D3D3A">job 2</text>

  <!-- job 1 — head of queue, accent highlight -->
  <rect x="380" y="110" width="70" height="100" rx="10"
        fill="#5B5FC7" stroke="#141413" stroke-width="2"/>
  <text x="415" y="164" text-anchor="middle" class="m" fill="#FAF9F5">job 1</text>

  <!-- arrow queue → worker -->
  <line x1="458" y1="160" x2="522" y2="160"
        stroke="#87867F" stroke-width="1.5" marker-end="url(#qa)"/>

  <!-- worker box -->
  <rect x="530" y="90" width="130" height="140" rx="10"
        fill="#E3DACC" stroke="#141413" stroke-width="2"/>
  <text x="595" y="156" text-anchor="middle" class="m" fill="#141413">worker</text>
  <text x="595" y="172" text-anchor="middle" class="m" fill="#87867F">pool=4</text>

  <!-- annotations -->
  <text x="415" y="234" text-anchor="middle" class="s" fill="#87867F">next to run</text>
  <text x="60"  y="270" class="s" fill="#87867F">Jobs are pulled FIFO; the worker leases one at a time.</text>
</svg>
```

**Accent highlight pattern:**
- Head-of-queue: `fill="#5B5FC7"` (accent), `stroke="#141413"`, `stroke-width="2"`, text `fill="#FAF9F5"`
- Standard job: `fill="#F0EEE6"` (gray-150), `stroke="#3D3D3A"` (gray-700), `stroke-width="1.5"`
- Worker/container: `fill="#E3DACC"` (oat), `stroke="#141413"`, `stroke-width="2"`

---

### 4b. Retry with Exponential Backoff

**Description:** Horizontal timeline with attempt markers. Failed attempts are open circles with an X mark (two crossing lines). Success is a filled olive circle with a checkmark. Dashed quadratic Bezier arcs span wait gaps with mono labels showing delay duration.

```html
<svg id="svg-retry" xmlns="http://www.w3.org/2000/svg"
     width="720" height="320" viewBox="0 0 720 320">
  <defs>
    <style>
      .m { font-family: ui-monospace, "SF Mono", Menlo, Consolas, monospace; font-size: 11px; }
      .s { font-family: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif; font-size: 12px; }
    </style>
  </defs>

  <!-- background -->
  <rect width="720" height="320" fill="#FAF9F5"/>

  <!-- timeline axis -->
  <line x1="60" y1="190" x2="660" y2="190" stroke="#D1CFC5" stroke-width="1.5"/>
  <text x="60"  y="214" class="s" fill="#87867F">t = 0</text>
  <text x="660" y="214" text-anchor="end" class="s" fill="#87867F">time</text>

  <!-- attempt 1 (fail): open accent circle + X mark -->
  <circle cx="100" cy="190" r="8" fill="#FAF9F5" stroke="#5B5FC7" stroke-width="2"/>
  <line x1="96" y1="186" x2="104" y2="194" stroke="#5B5FC7" stroke-width="1.5"/>
  <line x1="104" y1="186" x2="96"  y2="194" stroke="#5B5FC7" stroke-width="1.5"/>
  <text x="100" y="222" text-anchor="middle" class="m" fill="#3D3D3A">try 1</text>

  <!-- attempt 2 (fail) -->
  <circle cx="180" cy="190" r="8" fill="#FAF9F5" stroke="#5B5FC7" stroke-width="2"/>
  <line x1="176" y1="186" x2="184" y2="194" stroke="#5B5FC7" stroke-width="1.5"/>
  <line x1="184" y1="186" x2="176" y2="194" stroke="#5B5FC7" stroke-width="1.5"/>
  <text x="180" y="222" text-anchor="middle" class="m" fill="#3D3D3A">try 2</text>

  <!-- attempt 3 (fail) -->
  <circle cx="330" cy="190" r="8" fill="#FAF9F5" stroke="#5B5FC7" stroke-width="2"/>
  <line x1="326" y1="186" x2="334" y2="194" stroke="#5B5FC7" stroke-width="1.5"/>
  <line x1="334" y1="186" x2="326" y2="194" stroke="#5B5FC7" stroke-width="1.5"/>
  <text x="330" y="222" text-anchor="middle" class="m" fill="#3D3D3A">try 3</text>

  <!-- attempt 4 (success): filled olive circle + checkmark -->
  <circle cx="600" cy="190" r="9" fill="#788C5D" stroke="#141413" stroke-width="1.5"/>
  <path d="M595,190 L599,194 L607,185"
        fill="none" stroke="#FAF9F5" stroke-width="1.5" stroke-linecap="round"/>
  <text x="600" y="222" text-anchor="middle" class="m" fill="#3D3D3A">try 4</text>

  <!-- dashed backoff arcs: quadratic Bezier, stroke-dasharray 4 4 -->
  <path d="M 100 182 Q 140 130 180 182"
        fill="none" stroke="#87867F" stroke-width="1.5" stroke-dasharray="4 4"/>
  <text x="140" y="124" text-anchor="middle" class="m" fill="#87867F">+1s</text>

  <path d="M 180 182 Q 255 108 330 182"
        fill="none" stroke="#87867F" stroke-width="1.5" stroke-dasharray="4 4"/>
  <text x="255" y="102" text-anchor="middle" class="m" fill="#87867F">+2s</text>

  <path d="M 330 182 Q 465  76 600 182"
        fill="none" stroke="#87867F" stroke-width="1.5" stroke-dasharray="4 4"/>
  <text x="465" y="70"  text-anchor="middle" class="m" fill="#87867F">+4s</text>

  <!-- annotation -->
  <text x="60" y="268" class="s" fill="#87867F">Each failure waits twice as long; jitter not shown.</text>
</svg>
```

**Key patterns:**
- Failure marker: `<circle>` with accent stroke (unfilled), plus two `<line>` elements offset ±4px for the X
- Success marker: `<circle>` filled olive, slate stroke; checkmark as `<path>` with `stroke-linecap="round"`
- Arc: `<path d="M x1 y Q cx cy x2 y">` quadratic Bezier, `stroke-dasharray="4 4"`, gray-500

---

### 4c. Fan-out / Fan-in

**Description:** "Butterfly" layout. Source node fans out to N parallel shard nodes, which converge back into a merge node. Demonstrates one-to-many and many-to-one arrow patterns with a shared marker.

```html
<svg id="svg-fanout" xmlns="http://www.w3.org/2000/svg"
     width="720" height="320" viewBox="0 0 720 320">
  <defs>
    <style>
      .m { font-family: ui-monospace, "SF Mono", Menlo, Consolas, monospace; font-size: 11px; }
      .s { font-family: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif; font-size: 12px; }
    </style>
    <marker id="fa" markerWidth="9" markerHeight="9" refX="7" refY="3.5"
            orient="auto" markerUnits="userSpaceOnUse">
      <path d="M0,0 L0,7 L7,3.5 z" fill="#87867F"/>
    </marker>
  </defs>

  <!-- background -->
  <rect width="720" height="320" fill="#FAF9F5"/>

  <!-- source box (oat fill, slate stroke 2px) -->
  <rect x="60" y="128" width="110" height="64" rx="10"
        fill="#E3DACC" stroke="#141413" stroke-width="2"/>
  <text x="115" y="158" text-anchor="middle" class="m" fill="#141413">source</text>
  <text x="115" y="172" text-anchor="middle" class="m" fill="#87867F">enqueue()</text>

  <!-- fan-out arrows -->
  <line x1="170" y1="160" x2="296" y2="64"  stroke="#87867F" stroke-width="1.5" marker-end="url(#fa)"/>
  <line x1="170" y1="160" x2="296" y2="128" stroke="#87867F" stroke-width="1.5" marker-end="url(#fa)"/>
  <line x1="170" y1="160" x2="296" y2="192" stroke="#87867F" stroke-width="1.5" marker-end="url(#fa)"/>
  <line x1="170" y1="160" x2="296" y2="256" stroke="#87867F" stroke-width="1.5" marker-end="url(#fa)"/>

  <!-- shard boxes (gray-150 fill, gray-700 stroke 1.5px) -->
  <rect x="300" y="44"  width="120" height="40" rx="10" fill="#F0EEE6" stroke="#3D3D3A" stroke-width="1.5"/>
  <text x="360" y="69"  text-anchor="middle" class="m" fill="#3D3D3A">shard 0</text>

  <rect x="300" y="108" width="120" height="40" rx="10" fill="#F0EEE6" stroke="#3D3D3A" stroke-width="1.5"/>
  <text x="360" y="133" text-anchor="middle" class="m" fill="#3D3D3A">shard 1</text>

  <rect x="300" y="172" width="120" height="40" rx="10" fill="#F0EEE6" stroke="#3D3D3A" stroke-width="1.5"/>
  <text x="360" y="197" text-anchor="middle" class="m" fill="#3D3D3A">shard 2</text>

  <rect x="300" y="236" width="120" height="40" rx="10" fill="#F0EEE6" stroke="#3D3D3A" stroke-width="1.5"/>
  <text x="360" y="261" text-anchor="middle" class="m" fill="#3D3D3A">shard 3</text>

  <!-- fan-in arrows -->
  <line x1="420" y1="64"  x2="546" y2="160" stroke="#87867F" stroke-width="1.5" marker-end="url(#fa)"/>
  <line x1="420" y1="128" x2="546" y2="160" stroke="#87867F" stroke-width="1.5" marker-end="url(#fa)"/>
  <line x1="420" y1="192" x2="546" y2="160" stroke="#87867F" stroke-width="1.5" marker-end="url(#fa)"/>
  <line x1="420" y1="256" x2="546" y2="160" stroke="#87867F" stroke-width="1.5" marker-end="url(#fa)"/>

  <!-- merge box (olive fill, slate stroke 2px) -->
  <rect x="550" y="128" width="110" height="64" rx="10"
        fill="#788C5D" stroke="#141413" stroke-width="2"/>
  <text x="605" y="158" text-anchor="middle" class="m" fill="#FAF9F5">merge</text>
  <text x="605" y="172" text-anchor="middle" class="m" fill="#E3DACC">await all</text>

  <!-- annotations -->
  <text x="232" y="42"  class="s" fill="#87867F">fan-out</text>
  <text x="486" y="42"  text-anchor="end" class="s" fill="#87867F">fan-in</text>
  <text x="60"  y="306" class="s" fill="#87867F">Parent job spawns N children, then blocks on a completion barrier.</text>
</svg>
```

**Box fill conventions across all three illustrations:**
| Box type | Fill | Stroke | stroke-width |
|---|---|---|---|
| Head / active | `#5B5FC7` (accent) | `#141413` | 2 |
| Source / container | `#E3DACC` (oat) | `#141413` | 2 |
| Standard item | `#F0EEE6` (gray-150) | `#3D3D3A` | 1.5 |
| Success / merge | `#788C5D` (olive) | `#141413` | 2 |

---

## 5. Arrow Markers (Reusable `<defs>` Snippet)

**Name:** Three-color SVG arrowhead set

**Description:** Reusable `<defs>` block defining gray, accent, and olive arrowhead markers. Drop this zero-dimension SVG at the top of your post's raw HTML block; all subsequent SVGs on the page can reference these markers by ID.

**When to use:** Any post with multiple SVG diagrams that share consistent arrow styling. Define once at the top, reference with `marker-end="url(#arrow)"` etc. throughout.

**Three variants:**
- `#arrow` — gray (neutral/default flow)
- `#arrow-accent` — electric indigo (highlighted or active paths)
- `#arrow-olive` — olive (success/completion paths)

All use `viewBox="0 0 10 10"`, `refX="9" refY="5"`, `6×6` render size, `orient="auto-start-reverse"`.

```html
<!-- Zero-size SVG — define once, reference anywhere on the page -->
<svg viewBox="0 0 0 0" width="0" height="0" style="position:absolute;overflow:hidden">
  <defs>
    <!-- Gray — neutral / default -->
    <marker id="arrow" viewBox="0 0 10 10" refX="9" refY="5"
            markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M0,0 L10,5 L0,10 z" fill="#87867F"/>
    </marker>

    <!-- Accent (electric indigo) — highlighted path -->
    <marker id="arrow-accent" viewBox="0 0 10 10" refX="9" refY="5"
            markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M0,0 L10,5 L0,10 z" fill="#5B5FC7"/>
    </marker>

    <!-- Olive — success / completion path -->
    <marker id="arrow-olive" viewBox="0 0 10 10" refX="9" refY="5"
            markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M0,0 L10,5 L0,10 z" fill="#788C5D"/>
    </marker>
  </defs>
</svg>
```

Usage on edges:
```html
<!-- Default gray arrow -->
<path d="M100,50 L200,50" stroke="#87867F" stroke-width="1.5" fill="none"
      marker-end="url(#arrow)"/>

<!-- Accent arrow (e.g., highlighted data path) -->
<path d="M100,50 L200,50" stroke="#5B5FC7" stroke-width="1.5" fill="none"
      marker-end="url(#arrow-accent)"/>

<!-- Olive arrow (success / completion) -->
<path d="M100,50 L200,50" stroke="#788C5D" stroke-width="1.5" fill="none"
      marker-end="url(#arrow-olive)"/>
```

For inline illustrations with `markerUnits="userSpaceOnUse"` (size independent of stroke scale):
```html
<marker id="qa" markerWidth="10" markerHeight="10" refX="8" refY="4"
        orient="auto" markerUnits="userSpaceOnUse">
  <path d="M0,0 L0,8 L8,4 z" fill="#87867F"/>
</marker>
```
