# Harness Architecture

> 参考: [Anthropic — Harness design for long-running apps](https://www.anthropic.com/engineering/harness-design-long-running-apps) 和 [OpenAI — Harness engineering](https://openai.com/index/harness-engineering/)

## 什么是 Harness

Harness 是一套结构化的脚手架系统，将复杂任务分解为可管理的组件，并编排多个专业化智能体协同工作。它解决两个核心问题：

1. **一致性退化**: 随着上下文窗口填满，模型会偏离目标
2. **自我评估偏差**: 智能体倾向于过度赞扬自己的输出

## 核心设计原则

### 1. Sprint-Based Ralph Loop

Generator 不一口气把整个 app 做完，而是一次只做一个 feature slice；Evaluator 在每个 slice 结束时验收打分；通过才进入下一个 sprint，不过关就返工。

```python
spec = Planner(user_requirement)

while 还有未实现功能:
    contract = Generator.propose(spec, completed)
    Evaluator.review_contract(contract)

    Generator.implement(contract)
    verdict = Evaluator.evaluate(implementation)

    if verdict == FAIL:
        # max 3 rounds of rework
        Generator.revise(feedback)
        verdict = Evaluator.evaluate(revision)

    mark_feature_completed()
```

### 2. 合同先行 (Contract Negotiation)

Sprint 的前置动作是 Generator 和 Evaluator 谈好"合同"：
- Generator 从 spec 中 pick 一个 feature，提出 sprint contract
- Evaluator 审合同：scope 是否合理？标准是否可测？依赖是否满足？
- 审通过后 Generator 才开始实现

### 3. 文件即记忆 (Context Reset Protocol)

每个 agent 每次执行都从干净上下文开始。它通过读取 `docs/exec-plans/` 中的文件来"回忆"之前发生了什么：

| Agent 需要知道什么 | 从哪里读 |
|-------------------|---------|
| 项目大蓝图 | `spec.md` |
| 哪些功能已完成 | `meta.json → spec_features` |
| 当前 sprint 要做什么 | `sprints/sprint-N/contract.md` |
| 上一轮反馈 | `sprints/sprint-N/iterations/round-M/feedback.md` |
| 之前 sprint 的实现决策 | `sprints/sprint-{1..N-1}/build-log.md` |

每一步的产出都必须写入记录系统——它们是下一个 agent 的输入。

### 4. 渐进式披露 (Progressive Disclosure)

每个智能体只接收其阶段所需的信息。就像为新队友做入职引导——提供产品原则、工程规范和团队文化，而不是一次性倾倒所有信息。

| 智能体 | 接收 | 不接收 |
|--------|------|--------|
| Planner | 用户需求、架构约束、核心理念、产品规格 | 代码、测试、构建产物 |
| Generator (合同) | spec, meta, 之前的 build-log | Evaluator 系统提示 |
| Generator (实现) | contract, 架构约束, 代码, 反馈 | Planner 推理过程 |
| Evaluator (审合同) | contract, spec, meta | Generator 系统提示 |
| Evaluator (验收) | contract, build-log, 代码, 测试 | Generator 推理过程 |

### 5. GAN 式生成-评估循环

Generator 生成、Evaluator 评估、反馈驱动改进。将生成和评估分离是关键——让生成者自我批评会失败，但调教外部评估者走向严格可以成功。

### 6. 硬阈值质量门 (Hard Threshold Quality Gates)

Sprint contract 规定可测试标准。Evaluator 必须验证所有标准。单一标准失败 = 整个 sprint 被拒。没有"差不多就行"。

## 三智能体架构

```
User Requirement (1-4 sentences)
        │
        ▼
┌─────────────┐
│   PLANNER   │  model: opus
│             │  输出: spec.md (大蓝图)
│  扩展需求    │  含 feature list + AC
│  定义功能    │  不规定实现方式
└──────┬──────┘
       │
       ▼
┌──────────────────────────────────────┐
│         SPRINT LOOP (Ralph Loop)     │
│                                      │
│  ┌───────────┐    ┌──────────────┐   │
│  │ GENERATOR │───→│  EVALUATOR   │   │
│  │  提合同    │    │  审合同       │   │
│  └───────────┘    └──────┬───────┘   │
│       │              APPROVED        │
│       ▼                              │
│  ┌───────────┐    ┌──────────────┐   │
│  │ GENERATOR │───→│  EVALUATOR   │   │
│  │  实现 sprint│   │  验收 sprint  │   │
│  └───────────┘    └──────┬───────┘   │
│       ▲              ┌───┴───┐       │
│       │             PASS   FAIL      │
│       │              │      │        │
│       │              │   返工(≤3轮)   │
│       │              │      │        │
│       └──────────────┘──────┘        │
│              │                       │
│         下一个 feature               │
└──────────────────────────────────────┘
        │
        ▼
    ALL DONE
```

## 不变量 (Invariants)

### Planner 不变量
1. 输出必须包含: 背景、目标、非目标、feature list (含 AC)、风险
2. 永远不指定如何实现
3. 每个 feature 可独立作为一个 sprint 实现
4. 每个 feature 有 3-5 条可测验收标准
5. Feature 按依赖和价值排序

### Generator 不变量
1. 一个 sprint 只做一个 feature
2. 先提合同，Evaluator 审过才写代码
3. 只实现合同规定的内容
4. 先写测试再实现 (TDD)
5. 交付前自我评估并记录到 build-log.md
6. 永远不修改 spec.md
7. 不对自己的工作做主观判断

### Evaluator 不变量
1. 只根据 sprint contract 评判
2. 每个判定必须引用具体证据
3. 永远不写或修改代码
4. 发现问题时不自我合理化
5. 单条标准失败 = 整个 sprint 失败
6. 审合同时拒绝 scope 过大、标准模糊、跳过依赖的合同
7. 每个失败必须包含可操作的修复建议

## 状态机

```
PLANNING
    │
    ▼
SPRINTING
    │
    ├─ CONTRACTING → CONTRACT_REVIEW → APPROVED
    │                                      │
    │                                      ▼
    │                               GENERATING → EVALUATING
    │                                      ↑          │
    │                                      │    ┌─────┴─────┐
    │                                      │   PASS      REVISING
    │                                      │    │       (max 3轮)
    │                                      │    │           │
    │                                      └────┘───────────┘
    │                                           │
    │                                      下一个 sprint
    │
    ▼
COMPLETED / FAILED
```

## 文件结构

```
docs/exec-plans/active/<run-id>/
  meta.json                          # 全局状态 + feature 完成度
  spec.md                            # Planner 产出: 大蓝图
  sprints/
    sprint-1/
      contract.md                    # Generator 提出 + Evaluator 审定
      contract-feedback.md           # (如被拒) Evaluator 的合同反馈
      build-log.md                   # Generator 实现日志 + 自评
      evaluation.md                  # Evaluator 验收结果
      iterations/
        round-2/
          feedback.md                # Evaluator 返工反馈
          changes.md                 # Generator 返工记录

.claude/
  agents/
    planner.md                       # Planner 智能体定义
    generator.md                     # Generator 智能体定义
    evaluator.md                     # Evaluator 智能体定义
  skills/
    harness/SKILL.md                 # 主编排 (/harness)
    planner/SKILL.md                 # 规划 (/planner)
    generator/SKILL.md               # 生成 (/generator)
    evaluator/SKILL.md               # 评估 (/evaluator)
```

## 模型选择策略

| 智能体 | 模型 | 理由 |
|--------|------|------|
| Planner | Opus | 深度推理扩展需求，识别边界情况 |
| Generator | Sonnet | 最佳编码模型，高吞吐量 |
| Evaluator | Opus | 严格批判性思维，抵抗自我合理化 |

## 关键洞察

1. **每个 harness 组件都编码了一个关于模型能力不足的假设**——这些假设值得定期压力测试。当模型能力提升时，架构应该简化。

2. **给智能体地图，而不是千页手册**。`AGENTS.md` 是目录，不是百科全书。

3. **人类的品味要持续回馈系统**。审查评论、重构 PR、用户反馈要沉淀为文档更新或工具编码。

4. **约束是速度的前提**——严格的边界和可预测的结构是智能体高效工作的先决条件。

5. **合同协商是 sprint 质量的前提**——如果"完成"标准不清楚，实现和验收都会走偏。
