# anthropic-html

[English](README.md)

一个用于生成 Anthropic 风格独立 HTML 图表文件的 [Claude Code](https://claude.ai/code) Skill，支持流程图、模块图、架构图和时序图。

基于 [anthropic/html-effectiveness](https://github.com/anthropics/anthropic-quickstarts/tree/main/html-effectiveness) 的设计模式：手写内联 SVG、Anthropic design tokens、零依赖、无需构建。

## 产物示例

- 每张图输出一个独立的 `.html` 文件
- 使用统一视觉规则的内联 SVG（大地色调色板、1.5/2px 描边、`rx=10` 圆角）
- 可选交互模式：点击节点展示详情侧边栏
- 任意浏览器直接打开，无需安装任何依赖

## 安装

将 `SKILL.md` 和 `REFERENCE.md` 复制到 Claude Code 的 skills 目录：

```bash
git clone git@github.com:L2ncE/anthropic-html.git ~/.claude/skills/anthropic-html
```

## 使用方法

需要画图时显式调用：

```
画一个用户登录流程图
画一个微服务架构图，带交互
帮我画这个模块的依赖关系图，输出到 ./docs/arch.html
```

Claude 会自动推断图表类型、从描述中推断文件名，并将文件写入当前工作目录。

## Design Tokens

| Token | 值 | 语义含义 |
|---|---|---|
| `--ivory` | `#FAF9F5` | 页面背景 |
| `--slate` | `#141413` | 主文本色 |
| `--clay` | `#D97757` | 焦点 / 当前元素 |
| `--olive` | `#788C5D` | 成功 / 完成 |
| `--rust` | `#B04A3F` | 错误 / 失败 |
| `--oat` | `#E3DACC` | 次级容器 |

## Skill 边界

| 需求 | Skill |
|---|---|
| 独立 `.html` 文件图表 | `/anthropic-html`（本 skill） |
| 嵌入 Markdown / Obsidian 的图表 | `/anthropic-mermaid` |

## 许可证

Apache 2.0 — 详见 [LICENSE](LICENSE)。
