# DayPalette Docs · AGENTS

本仓库（`daypalette-docs`）是 DayPalette 的**顶层共享文档仓**，承接四仓共用的：

- 业务概念与内容系统定义（`product/`）
- 资产生产、策展、校验、发布的操作流程（`operations/`）
- 多仓边界与协作关系（`architecture/`）
- 跨端共享设计语言（`design/`）

## 写作边界

仅写**跨仓共识**。以下内容应留在各自实现仓：

- HarmonyOS / ArkUI 专属实现 → `day_palette`
- Web 工作台内部实现 → `daypalette-palette-workbench`
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
- 跨仓变更时，本仓通常作为**收口环节**最后提交（参见共享版 §6）。

## 不提交清单

- 本地编辑器缓存、`.DS_Store`
- 个人草稿（请放在工作区 `/tmp` 或本地分支，不进 main）
