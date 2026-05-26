# Anthropic HTML — Reference

## § CSS tokens (paste into every `<style>` block)

```css
:root {
  --ivory:    #FAF9F5;
  --slate:    #141413;
  --clay:     #D97757;
  --clay-d:   #B85C3E;
  --oat:      #E3DACC;
  --olive:    #788C5D;
  --rust:     #B04A3F;
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
  color: var(--slate);
  font-family: var(--sans);
  -webkit-font-smoothing: antialiased;
  padding: 56px 24px 96px;
}
.sheet { max-width: 980px; margin: 0 auto; }
header { margin-bottom: 40px; }
.eyebrow {
  font-family: var(--mono);
  font-size: 11px; letter-spacing: 0.08em;
  text-transform: uppercase; color: var(--gray-500); margin-bottom: 10px;
}
h1 {
  font-family: var(--serif); font-weight: 500;
  font-size: 34px; letter-spacing: -0.01em; margin-bottom: 12px;
}
.lead { font-size: 14.5px; line-height: 1.6; color: var(--gray-700); max-width: 640px; }
.canvas {
  border: 1.5px solid var(--gray-300); border-radius: 14px;
  background: #fff; padding: 28px; overflow-x: auto;
}
svg.diagram { display: block; width: 100%; height: auto; }
```

---

## § SVG visual rules

### Color semantics
| Token | Meaning | Fill | Stroke |
|---|---|---|---|
| `--clay` | focus / active / current step | `rgba(217,119,87,0.12)` | `var(--clay)` 2px |
| `--olive` | success / done / healthy | `rgba(120,140,93,0.12)` | `var(--olive)` 1.5px |
| `--rust` | error / failure / rollback | `rgba(176,74,63,0.10)` | `var(--rust)` 1.5px |
| `--gray-150` | neutral process step | `#F0EEE6` | `var(--gray-300)` 1.5px |
| `--oat` | secondary container / worker | `#E3DACC` | `var(--slate)` 2px |
| white | default panel / decision | `#ffffff` | `var(--gray-300)` 1.5px |

### Shape rules
- Default: all `<rect>` use `rx="10"`
- Terminal / pill shapes (start/end nodes): `rx="22"` — overrides the default
- Decision diamonds: `<path d="M cx,y1 L x2,cy L cx,y3 L x1,cy Z"/>`
- Stroke widths: **1.5px** neutral, **2px** emphasized (containers, current step)
- **No drop shadows. No gradients. No images.**

### Typography in SVG
```
Node interior labels:   font-family mono, font-size 12, fill var(--slate)
Node sub-labels:        font-family mono, font-size 10, fill var(--gray-500)
Outside annotations:    font-family sans, font-size 12, fill var(--gray-500)
Edge labels (yes/no):   font-family sans, font-size 10, fill matching edge color
```
Always use `text-anchor="middle"` for centered labels.

### Z-order rule
Draw **edges first**, then **nodes** — SVG paints in document order, nodes must cover edge endpoints.

---

## § Arrowhead defs (paste into `<defs>`)

```xml
<defs>
  <!-- neutral gray arrow -->
  <marker id="arrow" viewBox="0 0 10 10" refX="9" refY="5"
          markerWidth="6" markerHeight="6" orient="auto-start-reverse">
    <path d="M0,0 L10,5 L0,10 z" fill="#87867F"/>
  </marker>
  <!-- olive (yes/success) arrow -->
  <marker id="arrow-olive" viewBox="0 0 10 10" refX="9" refY="5"
          markerWidth="6" markerHeight="6" orient="auto-start-reverse">
    <path d="M0,0 L10,5 L0,10 z" fill="#788C5D"/>
  </marker>
  <!-- rust (no/failure) arrow -->
  <marker id="arrow-rust" viewBox="0 0 10 10" refX="9" refY="5"
          markerWidth="6" markerHeight="6" orient="auto-start-reverse">
    <path d="M0,0 L10,5 L0,10 z" fill="#B04A3F"/>
  </marker>
</defs>
```

Edge classes:
```xml
<path class="edge"     d="..." marker-end="url(#arrow)"/>        <!-- neutral -->
<path class="edge yes" d="..." marker-end="url(#arrow-olive)"/>  <!-- yes/pass -->
<path class="edge no"  d="..." marker-end="url(#arrow-rust)" stroke-dasharray="4 4"/>
```

---

## § Node sizing guidelines

Consistent sizes prevent overlap. Use these as defaults:

| Node type | Recommended size | Notes |
|---|---|---|
| Process box | `width=200 height=48` | Two lines of text |
| Terminal pill | `width=160 height=44` | `rx=22` |
| Decision diamond | 84px wide × 64px tall | Use `<path>` |
| Swimlane column | `width=200` | Add padding 24px |
| Module box | `width=120–160 height=40–60` | Adjust to label length |
| Sequence lifeline | 2px vertical line, 120px apart | Horizontal gap between actors |

**ViewBox sizing**: Add 40px padding on all sides beyond outermost nodes. Top-down flowchart with 10 nodes at 80px spacing → height ≈ 10×80 + 80 = 880px.

---

## § Coordinate planning (avoid overlaps)

Before writing SVG, sketch a grid:

```
Top-down flowchart:
  cx = viewBox_width / 2  (center axis)
  node_y: start at 40, increment by (node_height + gap)
  gap between nodes: 36px minimum, 52px when edge label needed

Left-right module diagram:
  columns at x = 80, 300, 520, 740  (220px apart)
  rows centered at y = 100, 200, 300...

Sequence diagram:
  lifelines at x = 100, 250, 400, 550  (150px apart)
  events at y = 80, 130, 180...  (50px apart)
  lifeline label rect: y=20, height=40
```

---

## § Interactive pattern (opt-in)

Only add when user explicitly asks for "带交互" / "可点击" / "interactive" / "clickable" / "with panel".

### Layout CSS addition
```css
.layout {
  display: grid;
  grid-template-columns: minmax(0,1fr) 300px;
  gap: 32px; align-items: start;
}
@media (max-width: 920px) { .layout { grid-template-columns: 1fr; } }
aside {
  position: sticky; top: 24px;
  border: 1.5px solid var(--gray-300); border-radius: 14px;
  background: #fff; padding: 20px 20px 22px;
}
aside .hint { font-size: 12px; color: var(--gray-500); margin-bottom: 14px; }
aside h3 { font-family: var(--serif); font-weight: 500; font-size: 19px; margin-bottom: 6px; }
aside .meta { font-family: var(--mono); font-size: 11px; color: var(--gray-500); margin-bottom: 14px; }
aside p { font-size: 13.5px; line-height: 1.6; color: var(--gray-700); margin-bottom: 12px; }
aside pre {
  font-family: var(--mono); font-size: 11.5px; line-height: 1.55;
  background: var(--gray-150); border: 1px solid var(--gray-300);
  border-radius: 8px; padding: 10px 12px; white-space: pre-wrap; color: var(--slate);
}
.node { cursor: pointer; transition: transform 120ms ease; pointer-events: all; }
.node:hover { transform: translateY(-1px); }
.node.active rect:not(.hit), .node.active path { stroke: var(--clay); stroke-width: 2; }
```

### HTML structure
```html
<div class="layout">
  <div>
    <div class="canvas">
      <svg class="diagram" viewBox="0 0 [W] [H]">
        <!-- each node pattern:
          <g class="node" data-k="slug">
            <rect class="hit" x="[x]" y="[y]" width="[w]" height="[h]"
                  fill="transparent" pointer-events="all"/>
            <rect x="[x]" y="[y]" width="[w]" height="[h]" rx="10" fill="..." stroke="..."/>
            <text ...>[Label]</text>
          </g>
          The .hit rect MUST match the visible rect's x/y/width/height exactly.
        -->
      </svg>
    </div>
  </div>
  <aside id="panel">
    <div class="hint">Click a node →</div>
    <h3 id="p-title"></h3>
    <div class="meta" id="p-meta"></div>
    <p id="p-body"></p>
    <pre id="p-code"></pre>
  </aside>
</div>
```

### JS snippet
```html
<script>
const DETAIL = {
  "slug": { title: "...", meta: "...", body: "...", code: "..." },
};
const nodes = document.querySelectorAll(".node");
const T = document.getElementById("p-title");
const M = document.getElementById("p-meta");
const B = document.getElementById("p-body");
const C = document.getElementById("p-code");
nodes.forEach(n => {
  n.addEventListener("click", () => {
    nodes.forEach(x => x.classList.remove("active"));
    n.classList.add("active");
    const d = DETAIL[n.dataset.k];
    if (!d) return;
    T.textContent = d.title;
    M.textContent = d.meta;
    B.innerHTML = d.body.replace(/\n/g, "<br>");  // body is AI-authored, not user input
    C.textContent = d.code;
  });
});
// activate first node by default
if (nodes[0]) nodes[0].dispatchEvent(new MouseEvent('click', {bubbles:true}));
</script>
```

---

## § JavaScript safety rules (interactive variant)

### Serializing data with CJK or special characters
**Always use `json.dumps()` — never hand-write JS string literals containing `"` or `\`.**

```python
import json
# Produces a valid JS string literal — handles ", \, newlines, CJK, emoji
json.dumps(value, ensure_ascii=False)
# e.g. json.dumps('用户说"确认"') → '"用户说\\"确认\\""'
```

Wrong (breaks JS when value contains `"`):
```js
body:"用户说"确认"，继续执行"  // ← SyntaxError
```
Right:
```js
body:"用户说\"确认\"，继续执行"  // ← escaped by json.dumps
```

### SVG `<g>` click events
SVG `<g>` elements do **not** support `.click()` (HTML-only method).

```js
// ✗ Wrong — silently fails on SVG elements
nodes[0].click();

// ✓ Correct
nodes[0].dispatchEvent(new MouseEvent('click', {bubbles: true}));
```

**Making the full node area clickable** requires a transparent hit rect — `pointer-events: all` on `<g>` alone is not enough (empty areas still miss clicks):

```xml
<!-- Inside each <g class="node" data-k="..."> — add as FIRST child -->
<rect class="hit" x="[same as visible rect]" y="[same]" width="[same]" height="[same]"
      fill="transparent" pointer-events="all"/>
```

The hit rect **must** match the visible rect's `x/y/width/height` exactly.
The `.node.active` CSS selector uses `:not(.hit)` to avoid stroking the invisible rect.

### `</script>` in inline data
`json.dumps()` does **not** escape `</script>`. If any data value could contain that string, add a replace:

```python
json.dumps(value, ensure_ascii=False).replace('</script>', '<\\/script>')
```

In practice, DETAIL values are AI-authored constants (not user input), so the risk is near-zero. But be aware of it.

---

## § Checklist before writing file

- [ ] viewBox dimensions account for all nodes + 40px margin
- [ ] Edges drawn before nodes in source
- [ ] All rects have `rx="10"` (or `rx="22"` for terminals)
- [ ] No external resources
- [ ] `<defs>` contains arrowhead markers if edges present
- [ ] Filename inferred from topic in snake-case or kebab-case
- [ ] **If interactive:** JS data serialized with `json.dumps()` — no raw `"` inside string literals
- [ ] **If interactive:** SVG nodes use `dispatchEvent` not `.click()`
- [ ] **If interactive:** each `<g class="node">` has a `.hit` rect as first child (same x/y/w/h as visible rect)
- [ ] **If interactive:** `.node.active` CSS selector uses `rect:not(.hit)` to avoid stroking the hit rect
