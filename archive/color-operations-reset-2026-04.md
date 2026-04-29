# 配色运营旧方案归档说明（2026-04-29）

## 1. 当前状态

自 2026-04-29 起，DayPalette 现有配色运营多仓方案整体归档。

本次归档明确包含以下仓库与其配套流程：

- `daypalette-color-assets`
- `daypalette-palette-workbench`

处理原则：

- 上述两仓不再属于当前规划，后续实现不得默认依赖它们存在。
- `source / curation-state / bundle / workbench` 相关旧文档仅保留为历史资料。
- 在新方案文档落盘前，AI Agent 不得基于这些旧文档推导新的仓库边界、脚本、数据真相源或提交流程。

## 2. 归档原因

- 配色运营方案即将重新规划。
- 继续把旧方案保留为现行真相源，会导致 AI 在后续实现时误判目录、流程与职责边界。

## 3. 本次归档覆盖范围

以下共享层文档已统一降级为历史记录：

- `architecture/repository-boundaries.md`
- `architecture/workbench-technical-architecture.md`
- `operations/color-asset-lifecycle.md`
- `operations/workbench-curation-workflow.md`
- `product/color-asset-master-plan.md`
- `product/color-asset-library.md`
- `product/workbench-product-spec.md`

另外，`operations/git-commit-workflow.md` 中所有涉及 `daypalette-color-assets` / `daypalette-palette-workbench` 的仓库规范、跨仓顺序与示例，也只保留为历史记录。

## 4. 新方案落盘前的约束

- `day_palette` 只保留 App 运行时事实、现有资源落点与历史兼容信息，不外推新的运营链路。
- `daypalette-docs` 当前只继续承载共享设计语言、通用产品方向、协作原则与历史归档说明。
- 若要重启配色运营体系，必须先在共享文档中写出新的仓库边界、数据真相源与操作链路，再允许 AI 基于新文档实现。

## 5. 对 AI Agent 的直接指令

在看到以下关键词时，必须先回到本文确认状态，再决定是否继续：

- `daypalette-color-assets`
- `daypalette-palette-workbench`
- `source/current/`
- `bundle/current/`
- `curation-state.v1.json`
- `build-bundle`
- `import-bundle-to-app`
- `workbench`

若用户没有先给出新的正式方案文档，这些关键词都应默认视为旧方案残留，而不是新实现的依据。