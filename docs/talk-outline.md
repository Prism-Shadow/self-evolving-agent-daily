# Talk Outline / 演讲大纲

PenguinHarness 架构讲解大纲（要讲的内容），对应 [penguin-harness-design](https://github.com/Prism-Shadow/penguin-harness-design) 的 [05-ARCHITECTURE.md](https://github.com/Prism-Shadow/penguin-harness-design/blob/main/specs/05-ARCHITECTURE.md)。

## 1. 核心抽象与闭环

1. **最基本的 agent 抽象：Human、Environment 和 LLM** — 三者互相之间如何交互形成闭环；一次运行与多次运行
2. **agent 的层级关系** — 三层结构：agent（持久化 Agent State）→ session（一次对话/运行单元）→ request（单轮 LLM 请求）；基础 API：`createAgent` 创建/加载 Agent，`agent.createSession` 创建 Session，`agent.resumeSession` 从 Trace 恢复；**Session 创建时的依赖**：必须指定 `workspaceDir`（工作目录）与 `modelId`（模型），`apiKey` / `baseUrl` 可选（回退链：显式传参 → Project 配置 → 环境变量）；恢复语义：Model 与 Workspace 沿用原 Session、不可更换；换模型 = 开新 Session 并沿用源 Workspace（文件可达才是真延续），不注入历史
3. **设计 OmniMessage** — 以及 append-only trace 作为唯一真相来源

## 2. 接口设计

4. **如何设计 LLM 接口** — 异步流式与中断
5. **如何设计 Environment 接口** — 异步流式与中断
6. **如何设计 Human 接口** — 审批

## 3. 最小的工具集

7. **文件操作部分**
8. **bash 操作**
9. **subagent 操作**
10. **多模态接口**

## 4. 上下文管理

11. **中断与重试消息的处理** — 中断（`[turn_aborted]`）：用户中断/不可重试失败时暂存 carry-over、下次运行补发，模型据标记续接、不重复执行已含结果的工具；重试（`[turn_retried]`）：timeout / malformed 时自动指数退避重连（上限 5 次），原样重发本轮输入（保持结构、多模态不丢失）并附已产出的思考与工具调用结果；与运行中插话（`[user_steering]`）的区分；LLMOutcome / `stop_reason`（aborted / failed / auth）决定中断善后路径
12. **压缩**
13. **steering**
14. **goal 与 Dynamic Workflow**

## 5. Agent 上下文

15. **系统提示词** — system prompt 模板（`system_config.yaml`）+ `AGENTS.md` 组合注入；占位符替换（`{{AGENTS_MD}}`、`{{VAULT_KEYS}}`、`{{SKILL_METADATA}}`、`{{PLATFORM}}`、`{{CWD}}` 等）；系统合成标记 `[tag]…[/tag]`（`[turn_aborted]` / `[context_summary]` / `[user_steering]`）；系统层级 Prompt 与用户自定义指令分离
16. **技能** — `agent_state/skills/<skill_name>/SKILL.md` 文件即真源，frontmatter 提供 metadata（name / description）；安装与更新 = 整目录覆盖；`[use_skills]` 来源块调用；依赖与解释器装入 `shared_env/` 跨 Session 复用
17. **记忆** — `agent_state/memory/` 下 Markdown 文件 + frontmatter（name / description / updated_at）；User memory（跨项目）与 Workspace memory（按项目）分区；`MEMORY.md` 索引；何时该记、何时不该记（代码与配置不记，只记非显然的决策与约定）
18. **credentials** — `agent_state/.vault.toml`（0600、明文落盘、接口层掩码）；按 Agent 隔离、注入本会话子进程环境变量（值不进模型上下文、不进 Trace）；`{{VAULT_KEYS}}` 只注入键名列表；禁读加固：系统 Prompt 禁止模型读取 `.vault.toml` 与 `.project_config.toml`，配置一律经 `penguin config` CLI；凭据更新经系统接口（`PUT /models` 广播 `credentials_updated`），不做热更新、对新建/恢复的 Session 生效

## 6. 界面与产品

19. **CLI 接口**
20. **Server 与 Web 设计**
