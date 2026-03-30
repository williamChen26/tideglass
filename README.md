# Tideglass

Tideglass 是一个 RSS 订阅与阅读应用的仓库起点，目标体验接近 Reader 一类产品：订阅稳定、整理轻量、阅读沉浸。

这个仓库的核心不是“先写很多代码”，而是先把智能体工作的支撑结构搭好：入口清晰、文档可导航、计划可追踪、约束可执行，然后再让产品代码在这套骨架上稳定生长。

## 核心原则

1. 人类掌舵，智能体执行。
2. `AGENTS.md` 是地图，不是百科全书。
3. 仓库是记录系统，重要上下文必须版本化落地。
4. 优先优化智能体可读性，而不是临时对话上下文。
5. 用可执行约束守住架构和质量，而不是只靠口头约定。

## 仓库地图

- `AGENTS.md`: 智能体和协作者的短入口。
- `ARCHITECTURE.md`: 顶层架构与约束。
- `docs/README.md`: 知识库总索引。
- `docs/design-docs/`: 设计理念与决策。
- `docs/product-specs/`: 产品规格与验收标准。
- `docs/exec-plans/`: 活跃计划、完成记录、技术债。
- `docs/generated/`: 生成型参考材料。
- `docs/references/`: 外部参考与内部约定摘录。
- `scripts/check_repo_docs.sh`: 仓库健康检查。

## 推荐工作流

1. 先在 `docs/product-specs/` 写需求或规格。
2. 复杂工作在 `docs/exec-plans/active/` 落执行计划。
3. 实现或修改代码时，同时更新相关文档。
4. 提交前运行 `./scripts/check_repo_docs.sh`。
5. 完成后把计划移动到 `docs/exec-plans/completed/`，并补技术债记录。

## 初始化检查

```bash
./scripts/check_repo_docs.sh
```

如果你要在这个仓库里继续搭业务代码，建议先补：

- 真实产品方向与范围
- 技术栈选择
- 第一份产品规格
- 第一份执行计划
