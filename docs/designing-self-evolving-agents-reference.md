# Designing Self-Evolving Agents — 内容参考

> 本文档是 [hiyouga/Designing-Self-Evolving-Agents](https://github.com/hiyouga/Designing-Self-Evolving-Agents) 仓库（《构建自进化的 LLM Agent》React + Vite 演示文稿）的文本内容参考。
>
> 来源仓库：<https://github.com/hiyouga/Designing-Self-Evolving-Agents>
> 作者：郑耀威（Yaowei Zheng），Founder@LlamaFactory, CTO@PrismShadow
> Deck 框架：基于 [Prism-Shadow/minimal-web-slides](https://github.com/Prism-Shadow/minimal-web-slides) 构建
> 内容语言：简体中文 / English 双语

## 主题

LLM Agent 的系统结构、Agent Harness 如何控制执行过程，以及如何把任务反馈转化为可持久化的 Agent 改进（自进化）。

## 幻灯片大纲（9 页）

| # | 中文标题 | English Title | 类型 |
|---|---------|---------------|------|
| 1 | 构建自进化的 LLM Agent | Building Self-Evolving LLM Agents | cover |
| 2 | Agent 的基本构成元素 | Core Components of an Agent | diagram |
| 3 | Agent 的内部状态 | Inside Agent State | diagram |
| 4 | Agent Harness 的定义 | What an Agent Harness Defines | diagram |
| 5 | Agent Harness 的演化 | How Agent Harnesses Evolve | diagram |
| 6 | 构建自进化的 LLM Agent | Building Self-Evolving LLM Agents | diagram |
| 7 | 评估基准是自进化 Agent 的最后一环 | Benchmarks Close the Loop for Self-Evolving Agents | diagram |
| 8 | LLM Agent 的工程实现 | Engineering LLM Agent Systems | diagram |
| 9 | Thank You | Thank You | closing |

---

## 1. 构建自进化的 LLM Agent（封面）

**Building Self-Evolving LLM Agents**

演讲者：郑耀威（Yaowei Zheng）
- Founder@LlamaFactory
- CTO@PrismShadow

---

## 2. Agent 的基本构成元素

**Core Components of an Agent / Agent 的基本构成元素**

Agent 系统由三个主要部分构成：

### Agent 内部状态（Agent State）

> 包含专属的知识和信息，引导 Agent 的每次推理过程。
> Holds task-specific knowledge and working data that condition each reasoning step.

- **LLM** — 负责自然语言的理解和生成 / Performs language understanding, reasoning, and generation
- **上下文（Context）** — 负责维护模型的输入和输出 Token / Stores the tokens currently visible to the model
- **工作区（Workspace）** — Agent 专属的工作区，包括 GitHub 仓库和 Skill 文件等等 / Persistent workspace for repositories, skills, memory, and generated artifacts

### 外部世界（External Environment）

> 所有外部环境的集合，Agent 通过工具可以观察和影响外部世界。
> Everything outside the agent that tools can read from or write to.

### Harness

> 一系列确定性的代码框架，处理 Agent 内部状态和外部世界的变化逻辑。
> Deterministic runtime code that mediates state transitions between the agent and its environment.

---

## 3. Agent 的内部状态

**Inside Agent State / Agent 的内部状态**

将 Agent 内部状态解释为从**持久状态（Persistent state）**到**瞬时计算（Ephemeral compute）**的三层结构：

| 层级 | 基本单位（Primitive） | 生命周期（Lifetime） | 说明 |
|------|----------------------|---------------------|------|
| 工作区（Workspace） | 文件或代码更改（Files and diffs） | 当前项目（Project lifetime） | Agent 专属的工作区，存放可复用能力 / Persistent workspace for reusable capabilities and artifacts |
| 上下文（Context） | Token | 当前对话 / 任务运行（Conversation or task run） | 模型单次可见的信息窗口，存放当前模型输入 / Model-visible window for instructions, history, and working inputs |
| LLM | Tensor | 当前 Token / 单次推理（Single inference step） | 预训练 Transformer 模型，生成下一个 Token 的概率分布 / Transformer models that produce the next-token distribution |

---

## 4. Agent Harness 的定义

**What an Agent Harness Defines / Agent Harness 的定义**

> Agent Harness 是一套代码框架，约束了 Agent 完成任务的过程与逻辑。
> An agent harness is the runtime that controls how an agent observes, acts, and updates state while completing tasks.

### 基本逻辑（Core Runtime Logic）

| 逻辑 | 代码 | 说明 |
|------|------|------|
| 观测逻辑（Observation） | `observe` | 外部环境如何构成模型输入 / How the environment is serialized into model input |
| 行动逻辑（Action） | `act` | 当前的模型输出如何改变外部环境 / How model outputs become tool calls or environment changes |
| 更新逻辑（State update） | `update` | 多次调用之间 Agent 内部状态（上下文、工作区）的变化 / How context, workspace, and other state persist across model calls |

### 涌现行为

> 基于这些基本逻辑，Agent 可以演化出更复杂的逻辑，比如规划（plan）、反思（reflect）、委派（delegate）等等。
> From these primitives, richer behaviors emerge: planning, reflection, delegation, memory updates, and more.

---

## 5. Agent Harness 的演化

**How Agent Harnesses Evolve / Agent Harness 的演化**

> “Agent Harness 在 LLM 应用的第一天就已经出现。”
> Agent harnesses have been part of LLM applications from day one.

| 时代 | 形态 | 说明 |
|------|------|------|
| 过去（Past） | 手动挡 Harness（Manual Harness） | 人类编排 Workflow，Agent 按照固定步骤调用模型、解析输出、使用工具 / Humans design the workflow; the harness calls the model, parses outputs, and executes tools in fixed steps |
| 现在（Now） | 半自动挡 Harness（Semi-Autonomous Harness） | LLM 自行决定下一步动作和工具调用，Harness 负责约束、执行和更新状态 / The LLM selects tools and next actions; the harness enforces constraints, runs tools, and updates state |
| 未来（Future） | ？ | （未定义 — 更多执行和改进闭环被自动化） |

---

## 6. 构建自进化的 LLM Agent

**Building Self-Evolving LLM Agents / 构建自进化的 LLM Agent**

> 自进化 Agent 就是通过 Harness 在 **Agent 内部状态** 发生**持久化**的更新。
> A self-evolving agent uses the harness to close the loop between agent state and the external environment, turning feedback into persistent updates.

### 进化闭环（Evolution Loop）

Harness 控制 Agent 进化流程，在 **Agent 内部状态** 与 **外部世界** 之间形成循环迭代：

- **输出方向（Actions and outcomes）**：Agent 内部状态 → 外部世界
- **反馈方向（Feedback and update signal）**：外部世界 → Agent 内部状态

### 内部状态的持久化更新方式

| 状态层 | 更新方式（中文） | 更新方式（English） |
|--------|-----------------|---------------------|
| 工作区（Workspace） | Skill 更新、Memory 更新 | Skill updates, Memory |
| 上下文（Context） | Prompt 优化 | Prompt updates |
| LLM | 模型参数优化 | Fine-tuning |

### 外部世界提供的反馈

- 任务记录（Task traces）
- 环境奖励（Environment rewards）
- 用户反馈（User feedback）

### 核心挑战

> “产生进化很简单，产生有效果的进化很难”
> "Making agents change is easy. Making them improve is hard."

---

## 7. 评估基准是自进化 Agent 的最后一环

**Benchmarks Close the Loop for Self-Evolving Agents / 评估基准是自进化 Agent 的最后一环**

Harness 控制自进化 Agent 的评估闭环：Harness 同时支配 **Agent 内部状态**、**外部世界** 与 **评估基准（Evaluation Benchmark）**。

三种 Harness 形态的职责对比：

| 职责 | 手动挡 Harness（Manual） | 半自动挡 Harness（Semi-Autonomous） | 全自动挡 Harness（Autonomous） |
|------|-------------------------|-------------------------------------|-------------------------------|
| LLM 职责（LLM Role） | 一轮对话结果最优 / Optimize one response | 一次任务结果最优 / Optimize one task run | 多次任务结果最优 / Optimize across task runs |
| Harness 职责（Harness Role） | 无 / None | 驱动模型行动 / Drive agent execution | 驱动 Agent 进化 / Drive agent improvement |
| 人类职责（Human Role） | 编排 Workflow / Orchestrate workflows | 设计行动逻辑 / Design control logic | 定义评估基准 / Define evaluation criteria |

要点：评估基准是有效自进化的必要条件；人类职责从编排 Workflow 转向定义评估标准。

---

## 8. LLM Agent 的工程实现

**Engineering LLM Agent Systems / LLM Agent 的工程实现**

> 第一原则：设计干净透明的统一接口
> Design principle: Use clean, transparent interfaces across the runtime

实用 Agent 系统需要处理的工程界面：

| 组成 | 中文 | English | 说明 |
|------|------|---------|------|
| LLM 调用 | LLM 调用 | LLM API | AgentHub 模型接口 / AgentHub model interface |
| 工具 | 工具 | Tooling | Bash 是最优解决方案，类似 Pi Agent / Bash-first tool interface, in the spirit of Pi Agent |
| 环境 | 环境 | Runtime | 真实 Linux 系统 / Real Linux execution environment |
| 观测 | 观测 | Observability | Trace 和状态捕获 / Trace and state capture |
| 评估 | 评估 | Evaluation | 基于 Benchmark 的反馈 / Benchmark-driven feedback |
| 用户 | 用户 | User layer | 人类意图和反馈 / Human intent and feedback |

---

## 9. Thank You

**Thank You**

联系信息（Follow me on / 关注我的账号）：
- X: <https://x.com/hiyouga_dev>
- GitHub: <https://github.com/hiyouga>
- LinkedIn: <https://www.linkedin.com/in/hiyouga/>

---

## 附：源仓库运行方式

```bash
npm install
npm run dev      # 本地预览，通常 http://localhost:5173
npm run build    # 构建
```

> 说明：源仓库 `src/content/slides/` 中还包含 `KeyPointsSlide.jsx` 与 `ArchitectureSlide.jsx`，它们是模板（minimal-web-slides）自带的示例页，未注册进正式 deck（见 `src/content/slides.jsx`），故不包含在上文大纲中。
