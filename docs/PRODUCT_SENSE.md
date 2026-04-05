# Product Sense

产品决策应尽量落成可验证的文字，而不是抽象偏好。

## 规格框架

每份产品规格（`product-specs/`）必须明确：

| 维度 | 问题 |
|------|------|
| 用户 | 谁会使用这个功能？ |
| 问题 | 他们当前遇到什么痛点？ |
| 时机 | 为什么现在做而不是以后？ |
| 非目标 | 什么明确不做？ |
| 完成标准 | 如何判断做完了？ |

## 与 Harness 的关系

Planner agent 在扩展需求时会参考 `product-specs/` 中的已有规格，确保新功能与产品方向一致。

规格流向：
```
product-specs/ (长期产品方向)
       ↓
  /planner 输入 (Planner 读取)
       ↓
  spec.md (大蓝图: feature list + AC)
       ↓
  sprint contract.md (每个 sprint 的"完成"标准, Generator 提出 + Evaluator 审定)
```

## 产品方向
