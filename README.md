# Self-Evolving Agent Daily

Notes, experiments, and reference materials on building self-evolving LLM agents.
构建自进化 LLM Agent 的日常笔记、实验与参考资料。

## The Core Loop / 核心闭环

![Self-evolution loop (English)](assets/self-evolution-loop.en.svg)

<details>
<summary>中文版 / Chinese version</summary>

![自进化闭环（中文）](assets/self-evolution-loop.zh.svg)

</details>

> **English:** The agent acts on the environment; feedback flows back as persistent updates to agent state. The harness orchestrates the loop.
>
> **中文：** Agent 作用于外部环境，反馈回流为对 Agent 内部状态的持久化更新，由 Harness 编排整个闭环。

## Contents / 内容

| Path / 路径 | Description / 说明 |
|---|---|
| [`docs/designing-self-evolving-agents-reference.md`](docs/designing-self-evolving-agents-reference.md) | Bilingual content reference for the talk "Building Self-Evolving LLM Agents"（《构建自进化的 LLM Agent》演示文稿双语内容参考） |
| [`assets/self-evolution-loop.en.excalidraw`](assets/self-evolution-loop.en.excalidraw) | Editable source of the loop diagram, English (open in [excalidraw.com](https://excalidraw.com))（闭环图可编辑源文件，英文） |
| [`assets/self-evolution-loop.en.svg`](assets/self-evolution-loop.en.svg) | Rendered loop diagram, English — default（闭环图渲染图，英文，默认） |
| [`assets/self-evolution-loop.zh.excalidraw`](assets/self-evolution-loop.zh.excalidraw) | Editable source of the loop diagram, Chinese (open in [excalidraw.com](https://excalidraw.com))（闭环图可编辑源文件，中文） |
| [`assets/self-evolution-loop.zh.svg`](assets/self-evolution-loop.zh.svg) | Rendered loop diagram, Chinese（闭环图渲染图，中文） |

## Structure / 仓库结构

```text
docs/        参考资料与文档 / reference materials
assets/      图表资源 / diagram assets
LICENSE      Apache License 2.0
```
