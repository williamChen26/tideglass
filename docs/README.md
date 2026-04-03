# Docs Index

`docs/` 是这个仓库的记录系统。

如果一个智能体无法在仓库中发现某条信息，那么对它来说，这条信息就不存在。

## 目录

- [`design-docs/index.md`](design-docs/index.md): 设计文档目录与核心理念。
- [`product-specs/index.md`](product-specs/index.md): 产品规格目录与验收入口。
- [`exec-plans/index.md`](exec-plans/index.md): Sprint 运行、执行计划、技术债。
- [`generated/README.md`](generated/README.md): 生成型工件说明。
- [`references/index.md`](references/index.md): 外部参考与内部摘录。

## 顶层专题

| 文档 | 职责 | 关联 |
|------|------|------|
| [`PLANS.md`](PLANS.md) | 计划管理原则，含 ralph loop 运行机制 | `exec-plans/` |
| [`DESIGN.md`](DESIGN.md) | 设计原则与判断框架 | `design-docs/` |
| [`QUALITY_SCORE.md`](QUALITY_SCORE.md) | 质量评分维度，含 Evaluator 评审标准 | `.claude/agents/evaluator.md` |
| [`PRODUCT_SENSE.md`](PRODUCT_SENSE.md) | 产品决策框架 | `product-specs/` |
| [`FRONTEND.md`](FRONTEND.md) | 前端约束与可验证性 | — |
| [`RELIABILITY.md`](RELIABILITY.md) | 可靠性预算与降级策略 | — |
| [`SECURITY.md`](SECURITY.md) | 安全边界与审计要求 | — |

## Harness 编排系统 (Ralph Loop)

本仓库的核心开发循环由 sprint-based 三智能体 harness 驱动：

```
需求 → /planner (大蓝图) → Sprint 循环 { /generator 提合同 → /evaluator 审合同 → /generator 实现 → /evaluator 验收 }
```

- 运行状态: `exec-plans/active/<run-id>/`
- 架构设计: [`design-docs/harness-architecture.md`](design-docs/harness-architecture.md)
- 智能体定义: `.claude/agents/{planner,generator,evaluator}.md`
- 技能入口: `.claude/skills/{harness,planner,generator,evaluator}/SKILL.md`

每个 sprint 产生: `contract.md → build-log.md → evaluation.md`

## 维护规则

1. 新增长期知识时，优先放入 `docs/` 而不是聊天记录。
2. 新建文档时，要把它链回对应的 `index.md`。
3. 已完成执行计划要移动到 `completed/`，不要长期堆在 `active/`。
4. 生成型文档要注明来源、生成方式和刷新时机。
5. 当文档约束被频繁违反时，应优先把它转成 lint、测试或脚本。
