# DayPalette Docs

DayPalette 顶层共享文档仓库。

> 归档提示：自 2026-04-29 起，旧配色运营多仓方案已整体归档，`daypalette-color-assets` 与 `daypalette-palette-workbench` 不再属于当前规划。统一入口见 [`archive/color-operations-reset-2026-04.md`](archive/color-operations-reset-2026-04.md)。在新方案落盘前，不要把 `source / bundle / workbench` 相关旧文档当成现行真相源。

这个仓库承接当前共享原则与已归档历史方案说明，优先放这些内容：

- 跨项目都成立的业务概念与内容系统定义
- 已确认仍有效的共享设计语言与通用协作原则
- 旧配色运营方案的归档说明与历史决策材料
- 需要由共享层先定义、再由实现仓落地的跨仓约束

不放在这里的内容：

- HarmonyOS / ArkUI 专属实现细节
- Web 工作台内部实现细节
- 单个仓库自己的构建、发布、测试脚本说明
- 只对某一端成立的 UI 组件实现规范

## 当前仓库关系

| 仓库 | 主要职责 |
| --- | --- |
| `day_palette` | HarmonyOS 移动端、运行时消费态资源、App 专属实现文档 |
| `daypalette-color-assets` | 已归档并删除；原负责配色资产 source / bundle 与策展状态文件 |
| `daypalette-palette-workbench` | 已归档并删除；原负责配色策展界面与校验 / 打包脚本 |
| `daypalette-docs` | 顶层共享产品 / 流程 / 架构 / 设计语言文档 |

## 目录

```text
daypalette-docs/
  architecture/
  design/
  operations/
  product/
```

## 当前仍可直接使用的文档

- `archive/color-operations-reset-2026-04.md`：旧配色运营方案归档入口与 AI 使用约束
- `design/shared-design-language.md`：跨移动端与 Web 的共享设计语言
- `operations/git-commit-workflow.md`：当前仍适用于 `day_palette` / `daypalette-docs` 的提交流程；旧 assets/workbench 规则仅保留历史记录
- `operations/remote-json-rollout-plan.md`：阶段 1 远端 JSON 覆盖的托管选型、版本边界与最小控制要求
- `product/content-system-overview.md`：配色内容系统与核心业务对象
- `product/smart-matching-strategy.md`：智能配第一阶段的保守版策略与运营维护口径
- `product/strategy/moat-and-monetization.md`：护城河、目标用户、价值与付费战略讨论
- `ai-workflow/collaboration-model.md`：共享 AI 协作模型与 Prompt 工作流通用原则

## 已归档的旧配色运营文档

以下文档保留为历史记录，不再作为新方案真相源：

- `architecture/repository-boundaries.md`
- `architecture/workbench-technical-architecture.md`
- `operations/color-asset-lifecycle.md`
- `operations/workbench-curation-workflow.md`
- `product/color-asset-master-plan.md`
- `product/color-asset-library.md`
- `product/workbench-product-spec.md`

## 文档治理原则

1. 共享原则先写在这里，再由各仓库写自己的实现映射。
2. 同一主题如果同时存在“共享层”和“平台层”，共享层优先收口到这里。
3. 具体实现若与共享原则冲突，应先修正文档边界，再改实现。 
