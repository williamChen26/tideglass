# Tideglass

Tideglass 是一个 RSS 订阅与阅读应用的仓库起点，目标体验接近 Reader 一类产品：订阅稳定、整理轻量、阅读沉浸。

这个仓库的核心不是"先写很多代码"，而是先把智能体工作的支撑结构搭好：入口清晰、文档可导航、计划可追踪、约束可执行，然后再让产品代码在这套骨架上稳定生长。

## 核心原则

1. 人类掌舵，智能体执行。
2. `AGENTS.md` 是地图，不是百科全书。
3. 仓库是记录系统，重要上下文必须版本化落地。
4. 优先优化智能体可读性，而不是临时对话上下文。
5. 用可执行约束守住架构和质量，而不是只靠口头约定。

## Harness 编排系统 (Ralph Loop)

本仓库配备三智能体 harness 编排系统，采用 sprint-based ralph loop:

```
需求 → Planner (大蓝图) → Sprint 循环 { Generator 提合同 → Evaluator 审合同 → Generator 实现 → Evaluator 验收 } → 交付
```

| 命令 | 作用 |
|------|------|
| `/harness <需求>` | 完整 ralph loop: 规划→sprint 循环→交付 |
| `/planner <需求>` | 仅规划阶段，生成 spec |
| `/generator contract <run-id>` | 提出下一个 sprint 合同 |
| `/generator build <run-id>` | 实现当前 sprint |
| `/evaluator contract <run-id>` | 审查 sprint 合同 |
| `/evaluator sprint <run-id>` | 验收 sprint 实现 |

详见 [`docs/design-docs/harness-architecture.md`](docs/design-docs/harness-architecture.md)。

## 仓库地图

- `AGENTS.md`: 智能体和协作者的短入口。
- `ARCHITECTURE.md`: 顶层架构与约束。
- `docs/README.md`: 知识库总索引。
- `docs/design-docs/`: 设计理念与决策。
- `docs/product-specs/`: 产品规格与验收标准。
- `docs/exec-plans/`: Sprint 运行状态、完成记录、技术债。
- `docs/references/`: 外部参考与内部约定摘录。
- `.claude/agents/`: Planner、Generator、Evaluator 智能体定义。
- `.claude/skills/`: Harness 技能入口。

## 推荐工作流

1. 先在 `docs/product-specs/` 写需求或规格。
2. 用 `/harness <需求>` 启动端到端 ralph loop。
3. 每个 sprint 自动: 提合同→审合同→实现→验收→下一个。
4. 人类随时可以查看 `docs/exec-plans/active/<run-id>/` 的状态。
5. 全部功能完成后，运行归档到 `docs/exec-plans/completed/`。

## 初始化检查

```bash
./scripts/check_repo_docs.sh
```
