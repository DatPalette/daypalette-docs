# DayPalette 多仓库边界

## 1. 目标

DayPalette 当前已经不再是单一移动端项目，而是四个职责不同但彼此联动的仓库：

- `day_palette`
- `daypalette-color-assets`
- `daypalette-palette-workbench`
- `daypalette-docs`

这份文档用于明确：

- 每个仓库存什么
- 哪个目录是真相源
- 哪些目录是生成物
- 变更应该从哪里发起

## 2. 仓库职责

### 2.1 day_palette

职责：

- HarmonyOS 移动端代码
- App 运行时消费态资源
- App 专属设计实现、架构与发布文档

边界：

- 默认不承担日常策展编辑职责
- `entry/src/main/resources/rawfile/palette-data/` 是移动端消费态输入，不是日常编辑真相源

### 2.2 daypalette-color-assets

职责：

- 承接配色资产编辑态真相源
- 承接策展状态文件
- 承接生成后的消费态 bundle

边界：

- `source/current/` 是当前 source 真相源
- `bundle/current/` 是生成物目录，不作为人工编辑入口
- 历史版本优先依赖 Git，而不是堆很多目录副本

### 2.3 daypalette-palette-workbench

职责：

- 提供配色策展界面
- 对 source 数据做预览、筛选、对比、标记
- 执行 validate / build-bundle / sync-app-source / import-bundle-to-app

边界：

- 不再长期存储 `sample-data/` 或本地 `bundle/`
- 页面与脚本直接读取 `daypalette-color-assets/source/current/`
- 生成结果只输出到 `daypalette-color-assets/bundle/current/`

### 2.4 daypalette-docs

职责：

- 管理跨项目共享的产品、流程、架构、设计语言文档
- 作为四仓之间的“公共规则层”

边界：

- 不承载平台专属实现说明
- 不替代各仓库自己的 README 与实现文档

## 3. 真相源表

| 主题 | 真相源位置 | 说明 |
| --- | --- | --- |
| 共享设计语言 | `daypalette-docs/design/` | 先定义跨端原则，再由各仓库实现映射 |
| 配色 source 数据 | `daypalette-color-assets/source/current/` | 日常策展与校验读取这里 |
| 策展状态 | `daypalette-color-assets/source/current/curation-state.v1.json` | 工作台运营层状态 |
| 消费态 bundle | `daypalette-color-assets/bundle/current/` | 由工作台脚本生成 |
| App 运行时消费资源 | `day_palette/entry/src/main/resources/rawfile/palette-data/` | 仅在明确导入时更新 |
| App 专属实现规范 | `day_palette/docs/` | HarmonyOS / ArkUI 专属 |

## 4. 标准变更路径

1. 在 `daypalette-color-assets/source/current/` 准备或重置 source 数据。
2. 在 `daypalette-palette-workbench` 中进行策展、预览、筛选与校验。
3. 通过 `pnpm build-bundle` 生成 `daypalette-color-assets/bundle/current/`。
4. 只有当移动端确认要消费这一版时，才执行 `pnpm import-bundle-to-app`。
5. App 版本发布时，移动端随版本带出这份 bundle。

## 5. 设计语言治理建议

共享设计语言应当放在 `daypalette-docs/design/`，原因是：

- 移动端与 Web 端都需要遵守同一套品牌气质与排版角色
- 共享层应该先定义“原则、语义、层级、动效节奏”
- 平台层再各自落到 ArkUI 组件令牌或 Web CSS 变量

因此建议采用两层文档：

- `daypalette-docs/design/`：共享设计语言与治理规则
- 各仓库内部设计文档：平台专属实现与组件细节
