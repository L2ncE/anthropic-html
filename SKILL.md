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
4. **Default: static SVG.** Only add interactive aside panel when user says "带交互" / "可点击" / "interactive" / "clickable" / "with panel"
5. **Open in browser + verify (always run this step)**
   - Use `mcp__chrome-devtools__*` tools to open the file; fallback to any other available browser MCP tool
   - **Static verify (all diagrams):** call `take_snapshot` (confirm header/svg/canvas nodes present) + `take_screenshot` (visually check: no node/label overlap, no clipping, viewBox padding ≥ 40px, no unexpected horizontal scroll). Fix issues by adjusting viewBox + coordinates.
   - **Interactive variant only (`<script>` block present):**
     1. Call `list_console_messages` — check for `error`-level entries
     2. Run behavior check via `evaluate_script`:
        - Click node index 0 → assert `p-title` text is non-empty
        - If ≥2 nodes exist: click node index 1 → assert `p-title` text changed from step above
        - Assert at least one node has class `active`
     3. If any check fails → analyse, fix HTML, reload, re-run checks. Repeat up to **2 times**
     4. If still failing after 2 retries → report to user and stop
   - **If no browser tool is available:**
     ```bash
     python3 -c "
     import re, sys, tempfile, subprocess, pathlib
     html = pathlib.Path('FILE').read_text()
     m = re.search(r'<script>(.*?)</script>', html, re.DOTALL)
     if not m: sys.exit(0)
     fd, tmp = tempfile.mkstemp(suffix='.js')
     import os; os.close(fd)
     pathlib.Path(tmp).write_text(m.group(1))
     r = subprocess.run(['node', '--check', tmp])
     os.unlink(tmp)
     sys.exit(r.returncode)
     " && echo 'JS syntax OK'
     ```
     Replace `FILE` with the actual HTML path. If node is also unavailable → skip lint, warn user to check browser console manually.

## Diagram types

| Type | Layout |
|---|---|
| Flowchart | Top-down: terminal → process boxes → diamond gates → edges |
| Module / dependency | Left-right or grid: labeled boxes + directional arrows |
| Architecture | Swimlane columns per layer, boxes inside, connecting lines |
| Sequence | Vertical lifelines, horizontal labeled arrows, time flows down |

**Disambiguation rule** (when description matches multiple types):
- "层 / layer / 系统边界 / service boundary / 服务关系" → Architecture
- "消息 / message / 往返 / request-response / 时间顺序 / timeline" → Sequence
- Still ambiguous → ask 1 clarifying question before writing

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
- Label node interiors with 12px mono; annotate outside with 12px sans `--gray-500`
- Default `rx="10"` on rects; terminal/pill shapes use `rx="22"` (overrides default)
- Stroke: 1.5px neutral, 2px emphasized containers
- No shadows, no gradients
