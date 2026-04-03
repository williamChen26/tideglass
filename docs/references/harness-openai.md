# OpenAI: Harness Engineering in an Agent-First World

- 来源: https://openai.com/index/harness-engineering/
- 摘录日期: 2026-03-31
- 适用: 本仓库的记录系统、智能体可读性、约束执行策略

## 核心要点

### 1. 人类掌舵，智能体执行
> "Humans steer. Agents execute."

人类工作在不同的抽象层: 确定优先级、将用户反馈转化为验收标准、验证结果。当智能体遇到困难时，问"缺什么能力"而不是"怎么更努力"。

### 2. 给地图，不给手册
> "Give Codex a map, not a 1,000-page instruction manual."

AGENTS.md 是目录（~100行），不是百科全书。详细知识在结构化 `docs/` 中。

一个庞大指令文件的问题:
- 上下文是稀缺资源，大文件挤占了任务和代码的空间
- 当所有内容都"重要"时，什么都不重要
- 单体手册立即腐烂
- 难以机械验证

### 3. 仓库是记录系统
> "From the agent's point of view, anything it can't access in-context while running effectively doesn't exist."

Slack 讨论、Google Docs、人脑中的知识对智能体不可见。只有仓库内的版本化工件才存在。

### 4. 渐进式披露
> "This enables progressive disclosure: agents start with a small, stable entry point and are taught where to look next."

就像为新队友做入职引导——提供产品原则、工程规范和团队文化，而不是一次性倾倒所有信息。

### 5. 约束是速度的前提
> "Agents are most effective in environments with strict boundaries and predictable structure."

机械执行的约束:
- 层间单向依赖（Types → Config → Repo → Service → Runtime → UI）
- 自定义 linter 的错误信息本身就是修复指令
- 结构化日志、命名约定、文件大小限制——静态强制

在人类优先工作流中，这些规则可能感觉迂腐。对智能体来说，它们是乘数效应。

### 6. 品味持续回馈系统
人类品味捕获一次，然后在每行代码上持续执行:
- 审查评论 → 文档更新
- 重构 PR → 工具编码
- 用户反馈 → 验收标准

当文档约束不够时，升级为代码约束。

### 7. 熵与垃圾回收
> "Technical debt is like a high-interest loan."

智能体复制仓库中已有的模式——包括次优模式。需要持续清理:
- "黄金原则"编码到仓库中
- 定期后台任务扫描偏差
- 多数修复可在一分钟内审查并自动合并

### 8. 智能体可读性是目标
> "The resulting code does not always match human stylistic preferences, and that's okay."

只要输出正确、可维护、对未来智能体运行可读，就达标。偏好依赖和抽象能够完全在仓库内被内化和推理。"无聊"的技术通常更容易被智能体建模。

## 对本仓库的影响

| 原则 | 实现 |
|------|------|
| 地图不是手册 | `AGENTS.md` ~50行，指向 `docs/` |
| 仓库是记录系统 | `docs/` 结构化目录，所有知识版本化 |
| 渐进式披露 | 每个 agent 只读取其阶段所需文件 |
| 约束机械执行 | `scripts/check_repo_docs.sh` + 未来 lint |
| 品味回馈 | Evaluator 质量观察 → `QUALITY_SCORE.md` |
| 垃圾回收 | 技术债追踪 + 定期清理 |
