<div align="center">

# anthropic-html

Generate Anthropic-style standalone HTML diagrams — flowcharts, module diagrams, architecture diagrams, and sequence diagrams.

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-skill-D97757.svg)](https://claude.ai/code)
[![中文文档](https://img.shields.io/badge/文档-中文-788C5D.svg)](README_zh.md)

</div>

---

Built on the design patterns from [anthropic/html-effectiveness](https://github.com/anthropics/anthropic-quickstarts/tree/main/html-effectiveness): hand-crafted inline SVG, Anthropic design tokens, zero dependencies, no build step.

## What it produces

- Single self-contained `.html` file per diagram
- Inline SVG with consistent visual rules (earth-tone palette, 1.5/2px strokes, `rx=10` rects)
- Optional interactive mode: click a node to reveal a details panel
- Works in any browser — nothing to install

## Installation

```bash
git clone git@github.com:L2ncE/anthropic-html.git ~/.claude/skills/anthropic-html
```

## Usage

Invoke explicitly when you need a diagram:

```
Draw a user login flowchart
Draw a microservice architecture diagram, with interactive nodes
Draw the dependency graph for this module, save to ./docs/arch.html
```

Claude will infer the diagram type, pick a filename from the topic, and write the file to your current working directory.

## Design tokens

| Token | Value | Semantic meaning |
|---|---|---|
| `--ivory` | `#FAF9F5` | Page background |
| `--slate` | `#141413` | Primary text |
| `--clay` | `#D97757` | Focus / active element |
| `--olive` | `#788C5D` | Success / done |
| `--rust` | `#B04A3F` | Error / failure |
| `--oat` | `#E3DACC` | Secondary container |

## Skill boundary

| Need | Skill |
|---|---|
| Diagram as standalone `.html` file | `/anthropic-html` (this skill) |
| Diagram embedded in Markdown / Obsidian | `/anthropic-mermaid` |

## License

Apache 2.0 — see [LICENSE](LICENSE).
