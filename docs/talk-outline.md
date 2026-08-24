# Talk Outline / 演讲大纲

PenguinHarness 架构讲解大纲（要讲的内容），对应 [penguin-harness-design](https://github.com/Prism-Shadow/penguin-harness-design) 的 [05-ARCHITECTURE.md](https://github.com/Prism-Shadow/penguin-harness-design/blob/main/specs/05-ARCHITECTURE.md)。

## 1. 核心抽象与闭环

1. **最基本的 agent 抽象：Human、Environment 和 LLM** — 三者互相之间如何交互形成闭环；一次运行与多次运行
2. **agent 的层级关系** — agent、session、request，以及基础 API 的定义
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

11. **压缩**
12. **steering**
13. **goal 与 Dynamic Workflow**

## 5. Agent 上下文

14. **系统提示词**
15. **技能**
16. **记忆**
17. **credentials**

## 6. 界面与产品

18. **CLI 接口**
19. **Server 与 Web 设计**
