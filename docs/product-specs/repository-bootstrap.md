# Repository Bootstrap Spec

## 背景

需要一个受 `harness-engineering` 启发的仓库起点，用来承载后续产品或代码开发。

## 目标

1. 提供短入口 `AGENTS.md`。
2. 建立结构化 `docs/` 作为记录系统。
3. 提供最小自动化检查，防止仓库骨架退化。

## 非目标

- 本次不绑定具体业务。
- 本次不引入具体应用框架。
- 本次不实现复杂 lint 或结构测试系统。

## 验收标准

1. 仓库存在 `README.md`、`AGENTS.md`、`ARCHITECTURE.md`。
2. `docs/` 具备索引、专题页、计划目录和技术债记录。
3. 存在可本地执行的仓库健康检查脚本。
4. 存在 CI 以运行该检查。

