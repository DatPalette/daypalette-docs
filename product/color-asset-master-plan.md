# 配色资产运营统一主文档（已归档）

> 归档状态：自 2026-04-29 起，本文与其所依赖的 `daypalette-color-assets` / `daypalette-palette-workbench` 方案已整体归档。统一入口见 [`../archive/color-operations-reset-2026-04.md`](../archive/color-operations-reset-2026-04.md)。
> 使用约束：本文仅保留 2026-04 之前的历史判断，不得作为新方案真相源。
> 来源：原 `day_palette/docs/product/strategy/color-asset-unified-master-plan.md`，2026-04-20 提升到顶层共享层。

## 快速导航

- 仓库边界与真相源：[`../architecture/repository-boundaries.md`](../architecture/repository-boundaries.md)
- 资产结构与字段：[`./color-asset-library.md`](./color-asset-library.md)
- 内容系统对象总览：[`./content-system-overview.md`](./content-system-overview.md)
- 资产生命周期：[`../operations/color-asset-lifecycle.md`](../operations/color-asset-lifecycle.md)
- 工作台策展流程：[`../operations/workbench-curation-workflow.md`](../operations/workbench-curation-workflow.md)
- 智能配策略：[`./smart-matching-strategy.md`](./smart-matching-strategy.md)
- 灵感入口（App 端专属）：[`../../day_palette/docs/product/strategy/inspiration-entry.md`](../../day_palette/docs/product/strategy/inspiration-entry.md)
- 策展工作台产品规格：[`./workbench-product-spec.md`](./workbench-product-spec.md)

> 完整上下文请以本仓 `daypalette-docs/` 下的文档为准；App / 资产仓 / 工作台仓在本文以外仅保留实现映射。

## 1. 文档定位

本文是 DayPalette 配色资产运营体系的**统一主文档**。

它合并了以下原本分散的信息：

- 产品层：为什么要做管理端、为什么要做资产包、前期运营载体是什么。
- 技术层：是否采用 monorepo、模块如何拆、数据状态如何分层、发布链路如何设计。
- 执行层：如何迁移当前工程、是否需要新建项目、Git 历史怎么处理。
- AI 协作层：如何把整个工程拆成可逐步执行的阶段，让 AI 可以按步骤落地实现。

如果后续要让 AI 接手整套工程，应优先阅读本文，再按文中的阶段计划逐步执行。

---

## 2. 当前问题与目标

## 2.1 当前问题

DayPalette 已经开始把配色资源迁移到结构化 JSON，但仍存在三个现实问题：

1. 配色资产运营还没有真正落到一个可视化管理端。
2. 数据更新流程仍然偏“工程师改文件”，而不是“主理人管理资产”。
3. 管理端、客户端、资产包、同步脚本之间的工程边界还没有统一到一个可执行方案里。

## 2.2 当前目标

在不租服务器、不做云存储、不上重 CMS 的前提下，建立一套前期可执行的配色资产运营体系。

目标包括：

1. 有一个真正可用的 Web 管理端。
2. 有一套结构化资产包真相源。
3. 有一套发布到客户端的本地链路。
4. 有一份 AI 可以照着落地的分步计划。

---

## 3. 一句话结论

DayPalette 前期应演进为一个**轻量 monorepo**，包含：

- 一个 Harmony 客户端
- 一个 React 管理端
- 一个配色资产包模块
- 一个 schema / validator 模块
- 一个 CLI 模块
- 一个可选的本地 bridge

同时坚持以下边界：

1. 客户端仍然只消费**导入后的内置 JSON 资源**。
2. 管理端编辑结果默认先存在**本地草稿库**。
3. “发布”通过**本地 bridge -> CLI** 完成，而不是浏览器直接改仓库文件。
4. 最终仍然随 App 版本发版，不做 runtime 远端更新。

---

## 4. 前期产品方案

## 4.1 前期运营载体是什么

前期运营载体不是：

- 线上 CMS
- 动态服务端
- 远端实时内容平台

前期运营载体是：

- 一个**本地可运行的 Web 管理端**
- 一组**结构化配色资产包**
- 一条**本地发布与导入链路**

## 4.2 管理端要解决什么问题

管理端首版不是为了做复杂后台，而是为了让你能通过界面完成这些动作：

1. 浏览基础色、配色盘、合集与文案。
2. 新增和修改资产。
3. 预览这些资产在移动端中的大致呈现。
4. 保存草稿。
5. 发布为正式资产包。
6. 同步到客户端工程。

## 4.3 管理端首版页面范围

建议首版至少包含以下页面：

1. Dashboard
2. Base Color 管理页
3. Palette 管理页
4. Collection 管理页
5. Locale 内容页
6. Validation / Publish 面板

### 4.3.1 Dashboard

展示：

- 基础色数量
- palette 数量
- collection 数量
- draft / approved / archived 统计
- 当前 bundleVersion
- 最近修改记录

### 4.3.2 Base Color 管理页

支持：

- 色块浏览
- 标签筛选
- 新建 / 编辑 / 归档
- 查看引用关系

### 4.3.3 Palette 管理页

支持：

- palette 卡片预览
- 三色角色编辑
- 场景 / 风格 / 安全等级设置
- 名称 / 描述 / Hero 文案编辑
- 移动端样式预览

### 4.3.4 Collection 管理页

支持：

- 新建专题
- 选择 palette 成员
- 拖拽排序
- 设置封面
- 编辑专题说明

### 4.3.5 Locale 内容页

支持：

- 场景 label/title
- palette 名称与描述
- collection 名称与描述
- 中英文对照编辑
- 翻译缺失提示

### 4.3.6 Validation / Publish 面板

支持：

- 查看校验错误
- 查看发布摘要
- 执行“发布资产包”
- 执行“同步到客户端”

---

## 5. 技术架构总览

## 5.1 是否采用 monorepo

建议：**采用 monorepo，但保持轻量。**

原因：

1. 管理端、客户端、资产包、CLI 和 schema 强相关。
2. 单仓库最适合当前单人维护。
3. 这样可以最大限度复用类型、校验规则和文档。

不建议一开始引入：

- Nx
- Turborepo
- Lerna

建议使用：

- `pnpm workspace`

## 5.2 推荐仓库结构

```text
day_palette/
  apps/
    mobile/
    admin/
  packages/
    color-assets/
    color-schema/
    color-cli/
  tools/
    local-bridge/
  docs/
  package.json
  pnpm-workspace.yaml
```

## 5.3 各模块职责

### apps/mobile

职责：

- Harmony 客户端
- 只消费导入后的 `rawfile/palette-data/*.json`
- 不负责编辑资产

### apps/admin

职责：

- 管理端 UI
- 草稿编辑
- 本地预览
- 发布与同步按钮入口

技术栈：

- React
- Vite
- Tailwind CSS
- shadcn/ui

### packages/color-assets

职责：

- 存资产真相源
- 存当前正式 bundle
- 存 manifest 与 bundleVersion

建议结构：

```text
packages/color-assets/
  source/
    base-colors.json
    palettes.json
    collections.json
    locale/
      zh-CN.json
      en-US.json
  bundle/
    current/
      base-colors.v1.json
      palettes.v1.json
      collections.v1.json
      locale/
        zh-CN.v1.json
        en-US.v1.json
      manifest.local.json
```

### packages/color-schema

职责：

- 统一 TypeScript 类型
- 统一 schema / zod 校验
- 统一 normalizer / validator

### packages/color-cli

职责：

- validate
- build-bundle
- publish
- sync-mobile

### tools/local-bridge

职责：

- 在本地开发态暴露轻量 Node API
- 接收管理端发布请求
- 转调 CLI

---

## 6. 两套工具链如何共存

## 6.1 Harmony 工具链

Harmony 客户端继续使用：

- `oh-package.json5`
- `ohpm`
- `hvigor`

这条链路不交给 pnpm 接管。

## 6.2 Node 工具链

管理端和 packages 使用：

- `package.json`
- `pnpm workspace`
- Node.js

## 6.3 必须守住的边界

1. 不让 Harmony runtime 直接依赖 Node workspace 包。
2. 不让 Node workspace 去控制 Harmony 构建过程。
3. Node 工具链只负责：生成、校验、同步 JSON 资源。

---

## 7. 数据状态分层

## 7.1 会话态

位置：

- React state

用途：

- 当前页面内的临时输入与交互状态

## 7.2 草稿态

位置：

- IndexedDB

用途：

- 存未发布的编辑结果
- 支持刷新恢复
- 支持本地草稿

建议字段：

- `draftId`
- `assetType`
- `payload`
- `updatedAt`
- `validationErrors`
- `isDirty`

## 7.3 发布态

位置：

- `packages/color-assets/source/`

用途：

- 经过规范化后的正式资产真相源

## 7.4 bundle 态

位置：

- `packages/color-assets/bundle/current/`
- 最终同步到客户端 `rawfile/palette-data/`

用途：

- 客户端运行时直接消费

## 7.5 这三层为什么不能混

如果草稿态、发布态和 bundle 态不分开，会直接导致：

1. 编辑中脏数据污染客户端正式资源。
2. 管理端 UI 字段被打进运行时 bundle。
3. 无法判断“当前正在编辑什么”和“客户端当前使用什么”。

---

## 8. 发布链路

## 8.1 浏览器不能直接做什么

浏览器前端不能直接：

- 执行本机 CLI
- 写本地仓库文件
- 做 git 写操作

所以发布按钮背后必须经过一个本地桥接层。

## 8.2 推荐调用链路

```text
Admin UI Button
  -> Local Bridge API
  -> child_process.spawn()
  -> color-cli publish
  -> 写入 color-assets/source 与 bundle/current
  -> 可选执行 color-cli sync-mobile
  -> 返回任务结果给 Admin UI
```

## 8.3 推荐 local bridge API

建议提供：

- `GET /health`
- `POST /publish`
- `POST /sync-mobile`
- `GET /jobs/:jobId`

## 8.4 推荐任务制而不是同步等待

建议管理端点击发布后：

1. 先创建 job
2. 再轮询 job 状态
3. 最终拿到：
   - success / failed
   - stdout / stderr 摘要
   - bundleVersion
   - 是否已 sync mobile

---

## 9. CLI 设计

推荐提供以下命令：

### 9.1 validate

```bash
pnpm color-cli validate
```

作用：

- 校验 source 数据
- 输出错误与警告

### 9.2 build-bundle

```bash
pnpm color-cli build-bundle
```

作用：

- 从 source 生成 bundle/current
- 生成 manifest.local.json

### 9.3 publish

```bash
pnpm color-cli publish
```

作用：

- validate
- build-bundle
- bump 版本号
- 生成发布摘要

### 9.4 sync-mobile

```bash
pnpm color-cli sync-mobile
```

作用：

- 将 bundle/current 同步到 mobile 工程资源目录
- 输出 diff 摘要

## 9.5 当前不要默认做的事

CLI 不应默认：

- 自动 commit
- 自动 push
- 自动发线上静态站点

这些动作后续再决定。

---

## 10. 管理端按钮语义

管理端至少区分三个动作：

### 10.1 保存草稿

- 写 IndexedDB

### 10.2 发布资产包

- 走 local bridge
- 调 CLI publish

### 10.3 同步到客户端

- 调 CLI sync-mobile

### 10.4 是否做“一键发布并同步”

可以做，但不建议 V1 只保留这一个动作。

V1 更推荐：

- 两个按钮拆开
- 后续再补组合按钮

---

## 11. 客户端同步策略

“同步最新版本资产包”在当前阶段是：

- 开发流程动作
- 发版前动作

而不是：

- App runtime 能力

当前推荐链路：

```text
Admin 发布 bundle
  -> CLI sync-mobile
  -> 覆盖 mobile/rawfile/palette-data/
  -> 真机验证
  -> 随 App 版本发布
```

当前不建议做 runtime 更新，原因是现在更需要先把：

- 资产结构
- 编辑体验
- bundle 生成
- 客户端映射

这四件事做稳。

---

## 12. 版本策略

建议 bundle 使用独立版本号，例如：

- `2026.04.09.1`

建议 `manifest.local.json` 至少包含：

- `schemaVersion`
- `bundleVersion`
- `exportedAt`
- `counts`

前期不建议在仓库里长期保留大量旧 bundle 文件目录。

更合适的方式是：

- 当前版本放 `bundle/current/`
- 历史版本通过 Git commit / tag / release 追踪

---

## 13. 项目迁移策略

你已经明确可以接受“重新开一个项目，再把移动端整个迁过去”。

这在技术上是可行的，但有两种不同路线。

## 13.1 路线 A：在当前仓库内原地迁移

做法：

- 保持当前 git 仓库不变
- 在仓库内重组目录为 monorepo
- 把现有 Harmony 工程逐步迁到 `apps/mobile/`

优点：

- Git 历史天然保留
- 风险最小
- 适合当前阶段

当前最推荐：

- **优先采用路线 A**

当前确认结果：

- **已确认采用“当前仓库内原地迁移”策略。**

## 13.2 路线 B：新建仓库，再迁移过去

做法：

- 新建 monorepo 仓库
- 把现有客户端工程拷过去
- 再逐步建立 admin / packages / tools

优点：

- 结构干净
- 不受当前仓库历史目录影响

缺点：

- Git 历史默认会断

## 13.3 新仓库能不能保留 git 历史

可以，但不是零成本。

常见方式：

1. 直接在原仓库内迁移
   - 历史天然完整保留
2. 用 `git filter-repo` 或 `git subtree split` 把现有工程历史抽出来，再导入新仓库
   - 能保留一部分历史
   - 但操作复杂度更高
3. 直接复制文件到新仓库
   - 最简单
   - 但历史基本丢失

如果你问“值不值得为了这件事折腾历史”，当前我的建议是：

- **不值得优先花时间在新仓库历史迁移上**。
- 如果没有强需求，直接原地迁移最稳。

---

## 14. AI 友好的总执行原则

如果后续要让 AI 分步实现整个工程，必须明确几条原则：

1. AI 每次只做一个阶段或一个步骤，不跨阶段乱改。
2. 先稳定目录与边界，再开始生成页面和脚本。
3. 先打通 data flow，再做漂亮 UI。
4. 先让 CLI 跑通，再做“按钮发布”。
5. 如果某一步需要迁移工程目录，应先完成人类确认，再继续下一步。

---

## 15. AI 分阶段实施总计划

下面这部分是给后续 AI 直接执行用的。

执行原则：

- AI 必须按 Phase 顺序执行。
- 一个 Phase 完成后，应停下来等待你确认，再继续下一阶段。
- 涉及工程迁移、目录移动、脚本写文件、CLI 改写时，优先做最小可回滚改动。

---

## Phase 0：迁移策略确认

### Step 0.1：确认采用哪条迁移路线

目标：

- 在“原地迁移”与“新仓库迁移”之间确认最终路径。

推荐：

- 默认采用“当前仓库内原地迁移”。

完成定义：

- 文档中明确最终路线。
- 若采用新仓库，记录是否放弃 Git 历史。

当前状态：

- 已确认采用“当前仓库内原地迁移”。

### Step 0.2：冻结当前客户端资源结构

目标：

- 明确当前客户端 `rawfile/palette-data/` 与 catalog 加载层是迁移基线。

完成定义：

- 记录现有目录与关键入口。

---

## Phase 1：建立 monorepo 骨架

### Step 1.1：引入 pnpm workspace

目标：

- 建立 `apps/`、`packages/`、`tools/` 顶层结构。

完成定义：

- 存在 `pnpm-workspace.yaml`
- 存在根 `package.json`

### Step 1.2：规划 mobile 工程落点

目标：

- 决定现有 Harmony 工程是立即迁到 `apps/mobile/`，还是先逻辑保留、物理后迁。

当前状态：

- 已确认采用“先建立 monorepo 骨架，Harmony 工程先原地保留，CLI 和 admin 稳定后再物理迁移”。

---

## 15.1 当前已确认事项

1. 采用**当前仓库内原地迁移**，不新开仓库作为默认路线。
2. 前期采用**轻量 monorepo**，而不是继续维持单一 Harmony 工程。
3. 前期仍以**客户端内置数据包**为正式发布源，不做 runtime 远端更新。
4. 管理端编辑结果先落在**本地草稿库**，不直接改仓库正式文件。
5. “发布”通过**本地 bridge -> CLI** 触发，不让浏览器直接写仓库文件。
6. `apps/mobile` 的物理迁移时机已确认：**先建立 monorepo 骨架，Harmony 工程先原地保留，CLI 和 admin 稳定后再物理迁移。**
7. `tools/local-bridge` 已确认**不进入 V1 主范围**；V1 先做 CLI，管理端先支持草稿与导出，bridge 作为后续阶段接入。
8. 管理端 V1 页面范围已确认先收敛为：**BaseColor / Palette / Publish 三页**。
9. 资产包历史版本策略已确认：**仓库内只保留 `bundle/current/`，历史交给 Git。**
10. 管理端发布动作已确认：**拆成“发布资产包”和“同步到客户端”两个按钮。**
11. CLI 第一批命令范围已确认：**先做 `validate / build-bundle / sync-mobile`，稳定后再补 `publish`。**
12. 管理端草稿粒度已确认：**采用资产级草稿，按 BaseColor / Palette / Collection 分开保存。**

## 15.2 已确认的默认执行口径

为避免后续 AI 在实现时反复停下来确认，当前默认执行口径统一如下：

1. monorepo 先搭骨架，再迁移动端工程。
2. V1 先打通 CLI，不先强依赖 local bridge。
3. 管理端首版只做最核心三页：BaseColor、Palette、Publish。
4. 发布与同步拆成两个独立动作，不做一键全自动。
5. 资产包历史交给 Git，而不是在仓库内长期保留多版本 bundle 目录。
6. 草稿以资产级粒度存于 IndexedDB，而不是整包单草稿。

推荐：

- 若当下时间有限，可先逻辑保留，后续再物理迁移。

### Step 1.3：建立 admin / color-assets / color-schema / color-cli 目录

目标：

- 把未来主要模块的目录骨架搭出来。

完成定义：

- 每个模块有最小 package 配置

---

## Phase 2：建立资产包真相源

### Step 2.1：定义 source 目录结构

目标：

- 在 `packages/color-assets/source/` 中建立正式资产真相源结构。

### Step 2.2：定义 bundle/current 目录结构

目标：

- 建立客户端正式 bundle 输出目录。

### Step 2.3：确定 manifest.local.json 结构

目标：

- 冻结 bundleVersion、schemaVersion、counts 等字段。

完成定义：

- 有可复用 manifest schema

---

## Phase 3：建立 schema 与校验体系

### Step 3.1：定义 TypeScript types

目标：

- 定义 BaseColor、Palette、Collection、LocaleContent、Manifest 等类型。

### Step 3.2：定义校验 schema

目标：

- 用 zod 或等价方案定义输入校验规则。

### Step 3.3：定义 normalizer 和 validator

目标：

- 把草稿态数据规范化为发布态数据。

完成定义：

- 可以独立运行校验并输出错误

---

## Phase 4：CLI 基础能力

### Step 4.1：实现 validate

目标：

- 能校验 source 数据

### Step 4.2：实现 build-bundle

目标：

- 能从 source 生成 bundle/current

### Step 4.3：实现 sync-mobile

目标：

- 能将 bundle/current 同步到客户端资源目录

### Step 4.4：实现 publish

目标：

- 串起 validate + build-bundle + version bump

完成定义：

- CLI 独立跑通，不依赖管理端 UI

执行备注：

- 当前已确认 `publish` 不属于 CLI 第一批必须完成命令；应在 `validate / build-bundle / sync-mobile` 稳定后再补。

---

## Phase 5：管理端 V1 骨架

### Step 5.1：初始化 admin 工程

目标：

- 使用 React + Vite + Tailwind + shadcn 初始化工程

### Step 5.2：建立路由和基础布局

目标：

- 建立 Dashboard / BaseColor / Palette / Collection / Locale / Publish 页面壳

执行备注：

- 当前已确认 V1 页面范围先收敛为 `BaseColor / Palette / Publish` 三页；Dashboard / Collection / Locale 可作为后续增强页延后。

### Step 5.3：建立设计令牌

目标：

- 尽量对齐移动端现有视觉语言

完成定义：

- 管理端已有统一基础样式和语义变量

---

## Phase 6：草稿态与编辑体验

### Step 6.1：接入 IndexedDB 草稿库

目标：

- 管理端支持本地持久化草稿

执行备注：

- 当前已确认采用资产级草稿，而不是整包单草稿。

### Step 6.2：实现 Base Color 编辑流

目标：

- 能新增、编辑、归档基础色

### Step 6.3：实现 Palette 编辑流

目标：

- 能编辑三色角色与文案

### Step 6.4：实现 Collection 编辑流

目标：

- 能维护专题集合与排序

### Step 6.5：实现 Locale 内容编辑流

目标：

- 能维护中英文内容文案

完成定义：

- 管理端能完整保存与恢复草稿

---

## Phase 7：校验与发布面板

### Step 7.1：实现 Validation 面板

目标：

- 展示 schema 错误、引用错误、命名冲突、翻译缺失

### Step 7.2：实现 Publish 面板

目标：

- 展示 bundleVersion、counts、变更摘要

### Step 7.3：先做“导出而非按钮发布”的兜底方案

目标：

- 即便本地 bridge 还没接通，也能先导出草稿快照或 bundle 文件

---

## Phase 8：本地 bridge

### Step 8.1：初始化 local-bridge

目标：

- 提供本地 Node API 服务

### Step 8.2：实现 `GET /health`

### Step 8.3：实现 `POST /publish`

### Step 8.4：实现 `POST /sync-mobile`

### Step 8.5：实现 `GET /jobs/:jobId`

完成定义：

- 管理端可通过本地 bridge 获得发布结果

执行备注：

- 当前已确认 local bridge 不进入 V1 主范围，应在 CLI 和管理端基础能力稳定后再接入。

---

## Phase 9：打通发布按钮

### Step 9.1：接通“发布资产包”按钮

目标：

- 点击后调用 bridge -> CLI publish

### Step 9.2：接通“同步到客户端”按钮

目标：

- 点击后调用 bridge -> CLI sync-mobile

### Step 9.3：补任务状态 UI

目标：

- 能展示 running / success / failed / logs

完成定义：

- 管理端完整打通保存草稿、发布、同步三个动作

---

## Phase 10：客户端资源接入验证

### Step 10.1：验证导入后的 JSON 可被当前客户端正确消费

### Step 10.2：验证 catalog loader / mapper 不受新链路影响

### Step 10.3：做一次真实 palette 修改演练

目标：

- 管理端改一组 palette
- 发布 bundle
- 同步 mobile
- 真机验证

---

## Phase 11：后续增强项

这些内容不属于 V1 必做：

1. runtime 远端更新
2. GitHub Pages 或对象存储分发
3. 自动 commit / push
4. 多人协作权限
5. 数据库存草稿
6. Electron 化管理端

---

## Phase 12：中间态任务最终收口

这一阶段用于处理前面各个 Phase 中那些“为了先跑通主链路，只做到中间态”的任务。

目标不是再扩功能面，而是把当前刻意延后的关键节点补到更稳定、可长期维护的最终态。

### Step 12.1：完成 mobile 工程物理迁移

前置条件：

- monorepo 骨架已经稳定
- CLI 和资产包链路已跑通
- 管理端至少完成 BaseColor / Palette / Publish 三页

目标：

- 将现有 Harmony 工程从当前目录结构正式迁移到 `apps/mobile/`

完成定义：

- `apps/mobile/` 成为客户端正式落点
- 原有根目录 Harmony 工程入口不再继续作为主入口维护
- 迁移后构建、资源加载、文档路径全部校正

### Step 12.2：补齐 CLI 的 publish 命令

前置条件：

- `validate / build-bundle / sync-mobile` 已稳定可用

目标：

- 将当前分散的发布动作正式收敛为 `publish` 命令

完成定义：

- `publish` 能串起校验、构建 bundle、版本号更新、发布摘要生成
- 命令输出结构稳定，可供 bridge 或管理端直接消费

### Step 12.3：接入 local bridge

前置条件：

- CLI 已稳定
- 管理端草稿态和发布面板已完成

目标：

- 让管理端从“可导出 / 可手动执行命令”升级到“本地一键调用 CLI”

完成定义：

- 存在 `GET /health`
- 存在 `POST /publish`
- 存在 `POST /sync-mobile`
- 存在 `GET /jobs/:jobId`
- 管理端可以展示任务状态与日志

### Step 12.4：补齐管理端剩余页面

前置条件：

- BaseColor / Palette / Publish 三页已经稳定

目标：

- 将当前延后的页面补齐到完整管理端形态

补齐范围：

- Dashboard
- Collection 管理页
- Locale 内容页

完成定义：

- 管理端从“最小可用编辑器”升级为“完整资产工作台”
- 资产统计、专题维护与中英文内容维护都能在 UI 中完成

### Step 12.5：补齐发布前检查与人工审核闭环

前置条件：

- CLI、bundle、管理端基础页已稳定

目标：

- 把当前偏工程化的链路收口成更稳定的日常运营流程

完成定义：

- 文档中存在正式发布前检查清单
- 管理端存在可读的 validation 摘要
- 一次真实修改可以按“草稿 -> 校验 -> 发布 -> 同步 -> 真机验证”完整跑通

### Step 12.6：做一次完整最终态演练

目标：

- 以一次真实配色资产更新为样本，验证最终态链路是否完整成立

建议演练内容：

1. 新增 1 个 BaseColor
2. 新增或修改 1 个 Palette
3. 更新 1 处 Locale 内容
4. 生成新 bundleVersion
5. 同步到客户端
6. 在真机或 IDE 内完成资源接入验证

完成定义：

- 不依赖人工临时补文件
- 不依赖跳过校验
- 不依赖在多个目录之间手工来回复制
- 能作为之后日常资产运营的标准样板流程

### Step 12.7：决定是否进入真正的阶段 2 能力

目标：

- 在最终态稳定后，再判断是否值得进入更重的后续能力

评估项：

1. 是否真的需要 runtime 远端更新
2. 是否真的需要保留仓库内版本快照目录
3. 是否真的需要多人协作与权限系统
4. 是否真的需要数据库草稿库
5. 是否真的需要 Electron 化或部署在线管理端

完成定义：

- 对这些增强项给出“进入 / 暂缓 / 放弃”的明确结论

---

## 16. 给 AI 的执行约束

后续如果你把这份文档交给 AI 落地，应要求它遵守以下规则：

1. 不要一开始同时搭管理端、bridge、CLI、客户端迁移。
2. 必须先完成 monorepo 骨架和数据边界。
3. CLI 跑通之前，不要先做“发布按钮”。
4. 先做草稿态，再做正式发布态。
5. 每完成一个 Phase，就停下来等人确认。
6. 如果某一步需要大规模移动目录，先说明迁移策略和风险。
7. 在 V1 阶段结束后，不要直接跳到 runtime 更新或更重的平台化能力，应先执行 `Phase 12`，把中间态任务补齐到最终态。

---

## 17. 暂定最终建议

当前最稳妥的路线不是新起一个复杂的在线内容平台，而是：

- 在当前仓库内逐步演进成轻量 monorepo
- 先建立管理端、资产包、schema、CLI
- 再补本地 bridge 与发布按钮
- 客户端继续通过导入后的内置 JSON 资源运行

如果时间很紧，优先级应该是：

1. 保住单文档真相源
2. 保住阶段边界
3. 先做 CLI
4. 再做按钮
5. 最后才考虑远端更新

这条路线最适合你当前“单人维护、无服务器预算、可以接受项目迁移、希望让 AI 分步完成”的现实条件。