# Anthropic: Harness Design for Long-Running Apps

- 来源: https://www.anthropic.com/engineering/harness-design-long-running-apps
- 摘录日期: 2026-03-31
- 适用: 本仓库的三智能体编排架构

## 核心要点

### 1. Harness 解决的两个问题
- **一致性退化**: 随着上下文窗口填满，模型偏离目标
- **自我评估偏差**: 智能体过度赞扬自己的输出，质量迭代受阻

### 2. 三智能体架构
- **Planner**: 1-4 句话需求 → 完整产品规格，不规定实现方式
- **Generator**: 迭代实现，对照合约规格工作，维护 git 版本控制
- **Evaluator**: 用 Playwright MCP 与真实应用交互，逐条验证合约标准

### 3. 文件即握手
> "Communication was handled via files: one agent would write a file, another agent would read it."

文件交接创造显式检查点，支持跨智能体上下文切换。

### 4. 合约协商
Generator 和 Evaluator 在构建前就"完成"标准达成一致。20+ 可测试标准，硬阈值——单条失败 = 整轮被拒。

### 5. 上下文重置 vs 压缩
- **压缩**: 原地总结早期对话。缺点: 智能体仍有"上下文焦虑"
- **重置**: 完全清空，结构化交接。代价: 编排复杂度。优势: 真正干净的状态

### 6. GAN 式循环
借鉴生成对抗网络: Generator 生成 → Evaluator 评估 → 反馈驱动改进。让生成者自我批评会失败，但调教外部评估者走向严格可以成功。

### 7. Evaluator 调教
开箱即用的 Claude 表现为"差 QA"——发现问题后又合理化。需要多轮调教:
1. 审查评估日志
2. 识别判断与期望的偏差
3. 更新 QA 提示词
4. 迭代直到判断对齐

### 8. 关键洞察
> "Every component in a harness encodes an assumption about what the model can't do on its own, and those assumptions are worth stress testing."

当模型能力提升时，架构应该简化。找到最简方案，只在需要时增加复杂度。

## 对本仓库的影响

| 原则 | 实现 |
|------|------|
| 三智能体架构 | `.claude/agents/{planner,generator,evaluator}.md` |
| 文件交接 | `docs/exec-plans/active/<run-id>/sprints/` 中的结构化文件 |
| 合约协商 | Generator 提 sprint contract, Evaluator 审合同 |
| 硬阈值质量门 | Evaluator 单条失败 = 整个 sprint 失败 |
| 上下文重置 | 每个 agent 独立上下文，从磁盘读取状态 |
| 反合理化 | Evaluator 不变量第4条: 发现问题时不自我合理化 |
