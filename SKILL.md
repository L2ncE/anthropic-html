---
name: anthropic-html
description: Generate standalone Anthropic-style HTML diagram files (flowcharts, module diagrams, architecture diagrams, sequence diagrams) using hand-crafted inline SVG and Anthropic design tokens. Output is a single self-contained .html file — no build step, no dependencies. Use when user says "画图"、"流程图"、"架构图"、"模块图"、"时序图"、"sequence diagram"、"flowchart" or any request for a diagram that should look like the Anthropic blog/docs visual style. Prefer over /anthropic-mermaid when a standalone file with precise visuals is needed. Replaces the deleted anthropic-diagram and floracat-architecture-diagram skills.
---

# anthropic-html

Generate a beautiful, self-contained HTML diagram in Anthropic's visual style.

## Workflow

1. Infer diagram type from user description → see type table below
2. Infer filename from topic (e.g. "登录流程" → `login-flow.html`), write to CWD unless user specifies
3. Write HTML with inline SVG using tokens from [REFERENCE.md](REFERENCE.md)
4. **Default: static SVG.** Only add interactive aside panel when user says "带交互" / "可点击" / "加说明"
5. Open in browser with `gstack` skill to verify — fix overlaps by adjusting viewBox + coordinates

## Diagram types

| Type | Layout |
|---|---|
| Flowchart | Top-down: terminal → process boxes → diamond gates → edges |
| Module / dependency | Left-right or grid: labeled boxes + directional arrows |
| Architecture | Swimlane columns per layer, boxes inside, connecting lines |
| Sequence | Vertical lifelines, horizontal labeled arrows, time flows down |

## HTML skeleton

```html
<!doctype html><html lang="en"><head>
<meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1">
<title>[Title]</title>
<style>/* PASTE token block from REFERENCE.md § CSS tokens */</style>
</head><body><div class="sheet">
  <header>
    <div class="eyebrow">[Category] · [Type]</div>
    <h1>[Title]</h1>
    <p class="lead">[One-sentence description]</p>
  </header>
  <div class="canvas">
    <svg class="diagram" viewBox="0 0 [W] [H]">
      <!-- PASTE arrowhead defs from REFERENCE.md § Arrowheads -->
      <!-- Draw edges BEFORE nodes so nodes render on top -->
    </svg>
  </div>
</div></body></html>
```

## Interactive variant (opt-in)

Wrap canvas in `<div class="layout">` alongside `<aside id="panel">`.
Each node gets `data-k="slug"`. JS updates aside on click.
→ See REFERENCE.md § Interactive pattern for the full snippet.

## Rules (always enforce)

- No external fonts, images, or scripts
- Edges drawn before nodes in SVG source (z-order)
- Label node interiors with 11px mono; annotate outside with 12px sans `--gray-500`
- `rx="10"` on all rects; `rx="22"` only on terminal/pill shapes
- Stroke: 1.5px neutral, 2px emphasized containers
- No shadows, no gradients
