# Plans

计划是一等工件，而不是临时草稿。

## 计划层级

| 层级 | 适用场景 | 存放位置 | 生命周期 |
|------|----------|----------|----------|
| **Harness Run** | 功能开发（需规划+构建+审查） | `docs/exec-plans/active/<run-id>/` | 自动管理 |
| **执行计划** | 跨模块复杂任务 | `docs/exec-plans/active/` → `completed/` | 手动归档 |
| **轻量计划** | 单模块小改动 | PR 描述或 commit message | 随 PR 关闭 |

## Harness 运行即计划

通过 `/harness` 或 `/planner` 启动的运行本身就是结构化计划：

1. **spec.md** — Planner 产出的大蓝图（背景、目标、非目标、feature list + AC、风险）
2. **sprints/sprint-N/contract.md** — Generator 和 Evaluator 逐 sprint 协商的"完成"标准
3. **meta.json** — 状态机跟踪，记录 feature 完成度、sprint 进度、判定结果

这比传统计划文档更强，因为它直接连接到执行和验证，且按 feature slice 逐步推进。

## 执行计划模板

跨模块复杂任务仍应在 `exec-plans/active/` 写完整计划：

```markdown
# Plan: <标题>

## 背景与目标
## 范围与非目标
## 实施步骤
## 风险与回滚思路
## 验证方法
## 决策日志

| 日期 | 决策 | 理由 | 替代方案 |
|------|------|------|----------|
```

## 适用原则

- 小任务写轻量计划，跨模块任务写完整执行计划
- 计划要包含范围、步骤、风险、验证方式和决策日志
- 完成后移动到 `completed/`，补技术债记录
- 如果一个计划反复修改超过 3 次，考虑拆分
