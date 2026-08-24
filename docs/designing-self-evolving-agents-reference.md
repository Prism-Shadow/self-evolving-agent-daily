# Designing Self-Evolving Agents — Content Reference

> This document is a content reference for the "Building Self-Evolving LLM Agents" deck (React + Vite web presentation) in the repository [hiyouga/Designing-Self-Evolving-Agents](https://github.com/hiyouga/Designing-Self-Evolving-Agents).
> 本文档是 [hiyouga/Designing-Self-Evolving-Agents](https://github.com/hiyouga/Designing-Self-Evolving-Agents) 仓库中《构建自进化的 LLM Agent》演示文稿（React + Vite Web 演示）的内容参考。
>
> Source repository / 来源仓库：<https://github.com/hiyouga/Designing-Self-Evolving-Agents>
> Deck framework / Deck 框架：built on [Prism-Shadow/minimal-web-slides](https://github.com/Prism-Shadow/minimal-web-slides)
> Content language / 内容语言：English + 简体中文 (bilingual)

## Topic / 主题

How LLM agents are structured, how agent harnesses control execution, and what it takes to turn task feedback into durable agent improvement (self-evolution).
LLM Agent 的系统结构、Agent Harness 如何控制执行过程，以及如何把任务反馈转化为可持久化的 Agent 改进（自进化）。

## Slide Outline / 幻灯片大纲（9 slides / 9 页）

| # | English Title | 中文标题 | Kind / 类型 |
|---|---------------|---------|-------------|
| 1 | Building Self-Evolving LLM Agents | 构建自进化的 LLM Agent | cover / 封面 |
| 2 | Core Components of an Agent | Agent 的基本构成元素 | diagram / 图示 |
| 3 | Inside Agent State | Agent 的内部状态 | diagram / 图示 |
| 4 | What an Agent Harness Defines | Agent Harness 的定义 | diagram / 图示 |
| 5 | How Agent Harnesses Evolve | Agent Harness 的演化 | diagram / 图示 |
| 6 | Building Self-Evolving LLM Agents | 构建自进化的 LLM Agent | diagram / 图示 |
| 7 | Benchmarks Close the Loop for Self-Evolving Agents | 评估基准是自进化 Agent 的最后一环 | diagram / 图示 |
| 8 | Engineering LLM Agent Systems | LLM Agent 的工程实现 | diagram / 图示 |
| 9 | Thank You | Thank You | closing / 结束 |

---

## 1. Building Self-Evolving LLM Agents（Cover / 封面）

Cover slide for the talk.
演讲封面。

---

## 2. Core Components of an Agent / Agent 的基本构成元素

An agent system consists of three major pieces.
Agent 系统由三个主要部分构成。

### Agent State / Agent 内部状态

> Holds task-specific knowledge and working data that condition each reasoning step.
> 包含专属的知识和信息，引导 Agent 的每次推理过程。

- **LLM** — performs language understanding, reasoning, and generation / 负责自然语言的理解和生成
- **Context（上下文）** — stores the tokens currently visible to the model / 负责维护模型的输入和输出 Token
- **Workspace（工作区）** — persistent workspace for repositories, skills, memory, and generated artifacts / Agent 专属的工作区，包括 GitHub 仓库和 Skill 文件等等

### External Environment / 外部世界

> Everything outside the agent that tools can read from or write to.
> 所有外部环境的集合，Agent 通过工具可以观察和影响外部世界。

### Harness

> Deterministic runtime code that mediates state transitions between the agent and its environment.
> 一系列确定性的代码框架，处理 Agent 内部状态和外部世界的变化逻辑。

---

## 3. Inside Agent State / Agent 的内部状态

Agent state as a stack from **persistent state** to **ephemeral compute**.
将 Agent 内部状态解释为从**持久状态**到**瞬时计算**的三层结构：

| Layer / 层级 | Primitive / 基本单位 | Lifetime / 生命周期 | Description / 说明 |
|--------------|----------------------|---------------------|--------------------|
| Workspace / 工作区 | Files and diffs / 文件或代码更改 | Project lifetime / 当前项目 | Persistent workspace for reusable capabilities and artifacts / Agent 专属的工作区，存放可复用能力 |
| Context / 上下文 | Tokens / Token | Conversation or task run / 当前对话或任务运行 | Model-visible window for instructions, history, and working inputs / 模型单次可见的信息窗口，存放当前模型输入 |
| LLM | Tensors / Tensor | Single inference step / 单次推理 | Transformer models that produce the next-token distribution / 预训练 Transformer 模型，生成下一个 Token 的概率分布 |

---

## 4. What an Agent Harness Defines / Agent Harness 的定义

> An agent harness is the runtime that controls how an agent observes, acts, and updates state while completing tasks.
> Agent Harness 是一套代码框架，约束了 Agent 完成任务的过程与逻辑。

### Core Runtime Logic / 基本逻辑

| Logic / 逻辑 | Code / 代码 | Description / 说明 |
|--------------|-------------|--------------------|
| Observation / 观测逻辑 | `observe` | How the environment is serialized into model input / 外部环境如何构成模型输入 |
| Action / 行动逻辑 | `act` | How model outputs become tool calls or environment changes / 当前的模型输出如何改变外部环境 |
| State update / 更新逻辑 | `update` | How context, workspace, and other state persist across model calls / 多次调用之间 Agent 内部状态（上下文、工作区）的变化 |

### Emergent Behavior / 涌现行为

> From these primitives, richer behaviors emerge: planning, reflection, delegation, memory updates, and more.
> 基于这些基本逻辑，Agent 可以演化出更复杂的逻辑，比如规划（plan）、反思（reflect）、委派（delegate）等等。

---

## 5. How Agent Harnesses Evolve / Agent Harness 的演化

> Agent harnesses have been part of LLM applications from day one.
> Agent Harness 在 LLM 应用的第一天就已经出现。

| Era / 时代 | Mode / 形态 | Description / 说明 |
|------------|-------------|--------------------|
| Past / 过去 | Manual Harness / 手动挡 Harness | Humans design the workflow; the harness calls the model, parses outputs, and executes tools in fixed steps / 人类编排 Workflow，Agent 按照固定步骤调用模型、解析输出、使用工具 |
| Now / 现在 | Semi-Autonomous Harness / 半自动挡 Harness | The LLM selects tools and next actions; the harness enforces constraints, runs tools, and updates state / LLM 自行决定下一步动作和工具调用，Harness 负责约束、执行和更新状态 |
| Future / 未来 | ? / ？ | Undefined — more of the execution and improvement loop becomes automated / 未定义 — 更多执行和改进闭环被自动化 |

---

## 6. Building Self-Evolving LLM Agents / 构建自进化的 LLM Agent

> A self-evolving agent uses the harness to close the loop between agent state and the external environment, turning feedback into persistent updates.
> 自进化 Agent 就是通过 Harness 在 **Agent 内部状态** 发生**持久化**的更新。

### Evolution Loop / 进化闭环

The harness orchestrates the evolution loop, iterating between **Agent State** and the **External Environment**.
Harness 控制 Agent 进化流程，在 **Agent 内部状态** 与 **外部世界** 之间形成循环迭代：

- **Output direction（输出方向）— Actions and outcomes / 输出动作和结果**：Agent State → External Environment
- **Feedback direction（反馈方向）— Feedback and update signal / 反馈和更新方向**：External Environment → Agent State

### Persistent Updates to Agent State / 内部状态的持久化更新方式

| State layer / 状态层 | Updates / 更新方式 |
|----------------------|--------------------|
| Workspace / 工作区 | Skill updates, Memory / Skill 更新、Memory 更新 |
| Context / 上下文 | Prompt updates / Prompt 优化 |
| LLM | Fine-tuning / 模型参数优化 |

### Feedback from the External Environment / 外部世界提供的反馈

- Task traces / 任务记录
- Environment rewards / 环境奖励
- User feedback / 用户反馈

### Core Challenge / 核心挑战

> "Making agents change is easy. Making them improve is hard."
> “产生进化很简单，产生有效果的进化很难”

---

## 7. Benchmarks Close the Loop for Self-Evolving Agents / 评估基准是自进化 Agent 的最后一环

The harness controls the evaluation loop of a self-evolving agent, governing **Agent State**, the **External Environment**, and the **Evaluation Benchmark**.
Harness 控制自进化 Agent 的评估闭环：Harness 同时支配 **Agent 内部状态**、**外部世界** 与 **评估基准**。

Role comparison across harness modes / 三种 Harness 形态的职责对比：

| Role / 职责 | Manual Harness / 手动挡 | Semi-Autonomous Harness / 半自动挡 | Autonomous Harness / 全自动挡 |
|-------------|-------------------------|-----------------------------------|-------------------------------|
| LLM Role / LLM 职责 | Optimize one response / 一轮对话结果最优 | Optimize one task run / 一次任务结果最优 | Optimize across task runs / 多次任务结果最优 |
| Harness Role / Harness 职责 | None / 无 | Drive agent execution / 驱动模型行动 | Drive agent improvement / 驱动 Agent 进化 |
| Human Role / 人类职责 | Orchestrate workflows / 编排 Workflow | Design control logic / 设计行动逻辑 | Define evaluation criteria / 定义评估基准 |

Key point: evaluation benchmarks are required for useful self-evolution; the human role shifts from workflow orchestration toward defining evaluation criteria.
要点：评估基准是有效自进化的必要条件；人类职责从编排 Workflow 转向定义评估标准。

---

## 8. Engineering LLM Agent Systems / LLM Agent 的工程实现

> Design principle: use clean, transparent interfaces across the runtime.
> 第一原则：设计干净透明的统一接口。

The engineering surface needed for practical agent systems / 实用 Agent 系统需要处理的工程界面：

| Component / 组成 | English / 英文 | 中文 | Description / 说明 |
|------------------|----------------|------|--------------------|
| LLM API / LLM 调用 | LLM API | LLM 调用 | AgentHub model interface / AgentHub 模型接口 |
| Tooling / 工具 | Tooling | 工具 | Bash-first tool interface, in the spirit of Pi Agent / Bash 是最优解决方案，类似 Pi Agent |
| Runtime / 环境 | Runtime | 环境 | Real Linux execution environment / 真实 Linux 系统 |
| Observability / 观测 | Observability | 观测 | Trace and state capture / Trace 和状态捕获 |
| Evaluation / 评估 | Evaluation | 评估 | Benchmark-driven feedback / 基于 Benchmark 的反馈 |
| User layer / 用户 | User layer | 用户 | Human intent and feedback / 人类意图和反馈 |

---

## 9. Thank You

Closing slide.
结束页。

---

## Appendix: Running the Source Repository / 附：源仓库运行方式

```bash
npm install
npm run dev      # local preview, usually http://localhost:5173 / 本地预览，通常 http://localhost:5173
npm run build    # build / 构建
```

> Note: `src/content/slides/` in the source repository also contains `KeyPointsSlide.jsx` and `ArchitectureSlide.jsx`. They are example pages shipped with the template (minimal-web-slides) and are not registered in the deck (see `src/content/slides.jsx`), so they are not included in the outline above.
> 说明：源仓库 `src/content/slides/` 中还包含 `KeyPointsSlide.jsx` 与 `ArchitectureSlide.jsx`，它们是模板（minimal-web-slides）自带的示例页，未注册进正式 deck（见 `src/content/slides.jsx`），故不包含在上文大纲中。
