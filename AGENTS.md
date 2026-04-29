# DayPalette Docs · AGENTS

本仓库（`daypalette-docs`）是 DayPalette 的**顶层共享文档仓**，承接当前共享原则与已归档历史方案说明：

- 业务概念与内容系统定义（`product/`）
- 资产生产、策展、校验、发布的历史流程归档（`archive/` / `operations/`）
- 多仓边界与协作关系（`architecture/`）
- 跨端共享设计语言（`design/`）

> 归档提示：自 2026-04-29 起，`daypalette-color-assets` 与 `daypalette-palette-workbench` 对应的旧配色运营方案已整体归档。统一入口见 [`archive/color-operations-reset-2026-04.md`](./archive/color-operations-reset-2026-04.md)。在新方案落盘前，不得把 `source / bundle / workbench` 相关旧文档当成现行真相源。

## 写作边界

仅写**跨仓共识**。以下内容应留在各自实现仓：

- HarmonyOS / ArkUI 专属实现 → `day_palette`
- 旧 Web 工作台内部实现曾位于 `daypalette-palette-workbench`；现已归档，未来以新方案文档为准
- 单仓的构建 / 发布 / 测试脚本说明 → 各自仓

## 治理原则

1. 共享原则**先写在本仓**，再由各实现仓写自己的映射文档。
2. 同一主题如果同时存在「共享层」与「平台层」，共享层优先收口到这里；实现仓只保留平台特例。
3. 实现若与共享原则冲突，应**先修文档边界，再改实现**。

## Git 提交规范

遵循 [`operations/git-commit-workflow.md`](./operations/git-commit-workflow.md)（`emoji + TYPE: 中文描述`）。

本仓特例：

- 所有变更默认使用 `📖 DOC:`。
- 若内容是**从子仓提升上来**的，commit 正文里注明来源路径，例如：
  
  ```text
  📖 DOC: 提升配色资产战略主文档到共享层

  来源：day_palette/docs/product/strategy/color-asset-unified-master-plan.md
  ```
- 跨仓变更时，本仓通常作为**收口环节**最后提交；旧 assets/workbench 顺序仅保留历史记录（参见共享版 §6）。

## 不提交清单

- 本地编辑器缓存、`.DS_Store`
- 个人草稿（请放在工作区 `/tmp` 或本地分支，不进 main）
