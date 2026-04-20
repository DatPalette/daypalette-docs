# DayPalette Docs

DayPalette 顶层共享文档仓库。

这个仓库承接四个项目之间的公共说明，优先放这些内容：

- 跨项目都成立的业务概念与内容系统定义
- 资产生产、策展、校验、发布的操作流程
- 多仓库边界、真相源与协作关系
- 需要在移动端与 Web 端保持一致的共享设计语言

不放在这里的内容：

- HarmonyOS / ArkUI 专属实现细节
- Web 工作台内部实现细节
- 单个仓库自己的构建、发布、测试脚本说明
- 只对某一端成立的 UI 组件实现规范

## 当前仓库关系

| 仓库 | 主要职责 |
| --- | --- |
| `day_palette` | HarmonyOS 移动端、运行时消费态资源、App 专属实现文档 |
| `daypalette-color-assets` | 配色资产 source / bundle、策展状态文件、资产版本提交 |
| `daypalette-palette-workbench` | 配色策展界面、校验与打包脚本 |
| `daypalette-docs` | 顶层共享产品 / 流程 / 架构 / 设计语言文档 |

## 目录

```text
daypalette-docs/
  architecture/
  design/
  operations/
  product/
```

## 当前首批文档

- `architecture/repository-boundaries.md`：四仓边界、真相源、生成物边界
- `operations/color-asset-lifecycle.md`：从 source 到 App 的完整操作链路
- `operations/workbench-curation-workflow.md`：工作台的策展职责与状态沉淀
- `operations/git-commit-workflow.md`：四仓共享的 Git 提交规范
- `design/shared-design-language.md`：跨移动端与 Web 的共享设计语言
- `product/content-system-overview.md`：配色内容系统与核心业务对象

## 文档治理原则

1. 共享原则先写在这里，再由各仓库写自己的实现映射。
2. 同一主题如果同时存在“共享层”和“平台层”，共享层优先收口到这里。
3. 具体实现若与共享原则冲突，应先修正文档边界，再改实现。 
