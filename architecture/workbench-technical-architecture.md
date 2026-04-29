# 配色策展工作台技术架构（已归档）

> 归档状态：自 2026-04-29 起，本文对应的 `daypalette-palette-workbench` / `daypalette-color-assets` 方案已整体归档。统一说明见 [`../archive/color-operations-reset-2026-04.md`](../archive/color-operations-reset-2026-04.md)。
> 使用约束：本文仅保留历史技术选型与边界记录，不得作为新方案真相源。
> 来源：原 `day_palette/docs/architecture/palette-curation-workbench-technical-implementation.md`，2026-04-20 提升到顶层共享层。

## 1. 文档定位

本文用于定义“配色策展工作台 MVP”在工程层面的推荐实现方式。

本文重点回答：

1. 首版应采用什么技术形态。  
2. 前端、数据层、校验层、预览层应如何分层。  
3. 资产仓库、工作台、移动端工程之间应如何协作。  
4. 哪些能力首版应直接实现，哪些应延后。  
5. 后续如果要封装为桌面端，当前实现应如何预留扩展空间。

---

## 2. 一句话结论

**首版应优先实现为一个本地运行的 Web 工作台，围绕“预览、筛选、比较、状态沉淀”构建前端界面；资产真相源继续保存在独立仓库；校验与 bundle 构建通过本地脚本或服务模块完成；后续如确有需要，再将同一套前端与服务模块封装为桌面端。**

这条路线的核心价值在于：

1. 先验证策展工作流。  
2. 控制首版复杂度。  
3. 保留未来桌面化空间。  
4. 不污染当前 HarmonyOS 工程。

---

## 3. 是否需要新开工程

结论：**需要，而且建议新开一个独立工程，不要直接塞进当前 DayPalette HarmonyOS 仓库。**

原因如下：

1. 当前仓库的主职责是 HarmonyOS App，本地工作台属于另一类产品。  
2. 工作台需要 Node.js 前端工具链、脚本、构建流程，与 `hvigor` 和 `ohpm` 不是同一体系。  
3. 如果把工作台直接塞进当前仓库，短期看似方便，长期会增加工程边界混乱和维护成本。  
4. 后续若工作台要桌面化，独立工程更容易迁移。

## 3.1 推荐仓库拆分

建议至少拆成以下两个仓库，理想情况下三个仓库：

1. `day_palette`  
也就是当前 HarmonyOS App 仓库。

2. `daypalette-palette-workbench`  
新开的策展工作台仓库。

3. `daypalette-color-assets`  
独立的配色资产仓库。

如果你暂时不想一口气拆成三个仓库，也可以先走两仓模式：

1. 当前 App 仓库不动。  
2. 新开一个工作台仓库，并在这个仓库内暂时带上本地 `assets-workspace/` 目录。

但从长期维护角度，三仓更清晰：

- App 仓库负责消费。  
- Workbench 仓库负责工具。  
- Assets 仓库负责真相源与版本化存储。

## 3.2 当前推荐结论

如果你今天就要开始做，我建议你直接：

**新开一个 `daypalette-palette-workbench` 工程。**

这个工程首版先只做：

- 本地 Web 工作台  
- 读取本地资产数据  
- 预览、筛选、对比、状态标记  
- 本地校验与导出脚本

远端 GitHub 资产仓库可以作为第二步接入。

---

## 4. 总体架构建议

建议拆成三个明确对象：

1. 配色资产仓库。  
2. 配色策展工作台。  
3. DayPalette 移动端工程。

## 4.1 配色资产仓库

职责：

- 保存编辑态真相源。  
- 保存发布态 bundle。  
- 保存 schema 快照和 manifest。  

建议结构：

```text
color-assets-repo/
  source/
    base-colors.json
    palettes.json
    collections.json
    locale/
  bundle/
    current/
      manifest.json
      base-colors.json
      palettes.json
      collections.json
      locale/
  schema/
  changelog/
```

## 4.2 配色策展工作台

职责：

- 读取 `source/`。  
- 生成预览。  
- 维护运营状态。  
- 做筛选、比较和基础编辑。  
- 执行校验与 bundle 构建。  
- 在需要时把结果推回资产仓库。

## 4.3 移动端工程

职责：

- 只消费 `bundle/current/`。  
- 不直接理解复杂编辑态字段。  
- 通过同步脚本更新本地资源目录。  
- 继续按既有构建方式打包和验收。

---

## 5. 技术栈建议

## 5.1 首版推荐技术栈

推荐首版采用：

- 前端框架：React  
- 构建工具：Vite  
- 语言：TypeScript  
- 样式方案：CSS Modules 或 Tailwind CSS 二选一  
- 组件基础层：自建轻量组件层或少量 Headless 组件  
- 列表渲染：支持虚拟滚动或虚拟网格  
- 数据校验：Zod 或 JSON Schema  
- 本地脚本：Node.js + TypeScript  
- 包管理：pnpm

## 5.2 为什么推荐这套组合

原因不是“它最潮”，而是它最适合你的任务：

1. React + Vite 足够快，适合做高频 UI 迭代。  
2. TypeScript 适合把运营字段、状态机和数据模型提前约束好。  
3. Node.js 脚本天然适合处理本地 JSON、校验、导出和 GitHub API。  
4. pnpm 适合后续如果你想拆 `app` 和 `scripts` 子包时继续扩展。

## 5.3 样式层推荐判断

如果你希望：

- 快速出高密度工作台界面  
- 快速做筛选栏、卡片网格、对比布局

可以选 Tailwind CSS。

如果你更希望：

- 样式边界更稳定  
- 后续维护更接近传统前端工程

可以选 CSS Modules。

当前我更倾向：

**首版用 Tailwind CSS 做布局效率更高，但不要依赖重后台模板。**

## 5.4 暂不建议的首版技术选择

首版不建议：

1. 一上来就 Electron。  
2. 一上来就 Tauri。  
3. 一上来就 Next.js。  
4. 一上来就完整低代码后台框架。  
5. 一上来就复杂数据库。

原因很简单：这些都不是你当前最核心的验证对象。

---

## 6. 首版技术形态选择

## 6.1 为什么先做本地 Web 工作台

首版更推荐本地 Web 工作台，而不是直接做桌面端壳，原因如下：

1. 你当前的核心任务是验证策展工作流，而不是验证桌面容器能力。  
2. 预览、筛选、对比、本地状态管理，本地 Web 已足够承载。  
3. 本地 Web 更利于高频迭代布局和预览模板。  
4. 如果一开始就上桌面端，主进程、文件系统、进程通信会额外增加复杂度。

## 6.2 为什么要预留桌面化空间

后续如果出现这些需求，则可以再桌面化：

1. 需要深度访问本地文件系统。  
2. 需要在应用内直接操作 git 或 GitHub 凭证。  
3. 需要离线草稿工作区。  
4. 需要内嵌原始数据编辑器。  

因此首版的工程组织应尽量让“前端 UI”和“资产处理服务”分层，避免未来迁移代价过高。

---

## 7. 推荐工程结构

建议工作台仓库结构如下：

```text
palette-curation-workbench/
  src/
    app/
    pages/
    widgets/
    features/
      overview/
      preview-grid/
      compare/
      review-detail/
      publish-check/
    entities/
      palette/
      collection/
      scene/
    shared/
      api/
      config/
      ui/
      utils/
      types/
  services/
    validator/
    bundler/
    repo-sync/
  schemas/
  scripts/
  public/
```

## 7.1 如果新开工程，建议目录结构

如果你按独立工程启动，建议结构直接定成下面这样：

```text
daypalette-palette-workbench/
  src/
  services/
  schemas/
  scripts/
  sample-data/
  package.json
  tsconfig.json
  vite.config.ts
  README.md
```

其中：

- `sample-data/` 用于早期没有接入资产仓库时的本地演示数据。  
- `services/` 和 `scripts/` 分开，是为了把“前端调用的逻辑”和“CLI 任务”分层。

## 7.2 `src/` 层职责

- `pages/`：页面级入口。  
- `widgets/`：页面区域组合。  
- `features/`：用户操作单元，如筛选、对比、状态标记。  
- `entities/`：与 `palette`、`scene` 等实体直接相关的模型和显示逻辑。  
- `shared/`：通用组件、类型、工具函数、配置。

## 7.3 `services/` 层职责

- `validator/`：执行 schema 校验、引用校验、命名校验。  
- `bundler/`：把编辑态数据转换为客户端消费态 bundle。  
- `repo-sync/`：负责读写本地工作区或远端资产仓库。

这样做的目的是把“UI 逻辑”和“资产处理逻辑”拆开。

---

## 8. 数据流设计

## 8.1 首版最小数据流

建议首版先跑通以下链路：

```text
本地 source JSON
  -> 工作台读取
  -> 渲染预览与筛选
  -> 更新 reviewStatus / notes / score
  -> 保存回本地 source JSON
  -> 运行 validate
  -> 运行 build-bundle
  -> 生成本地 bundle 输出
```

这条链路先不依赖 GitHub，就能验证你最核心的工作流。

## 8.2 完整目标数据流

后续完整链路建议如下：

```text
GitHub 资产仓库
  -> pull-assets 到本地工作区
  -> 工作台读取 source/
  -> 预览 / 筛选 / 比较 / 标记
  -> 保存回 source/
  -> validate-assets
  -> build-bundle
  -> publish-assets 到 GitHub 资产仓库
  -> App 仓库 pull latest bundle
  -> 同步到 Harmony 资源目录
```

## 8.3 三种数据状态

建议明确区分三种状态：

1. 工作态  
当前页面中临时筛选、临时选中、当前对比池。

2. 编辑态  
保存于 `source/` 的真实资产与运营字段。

3. 发布态  
保存于 `bundle/` 的客户端消费数据。

只要这三层不混，后续扩展就不会太乱。

---

## 9. 前端技术建议

## 9.1 页面结构建议

首版建议至少实现这几个页面：

1. `OverviewPage`  
2. `PreviewGridPage`  
3. `ComparePage`  
4. `ReviewDetailPage`  
5. `PublishCheckPage`

## 9.2 状态管理建议

建议使用轻量状态方案，不必一开始就上重型全局状态库。

比较稳妥的组合是：

1. React 本地状态管理页面 UI。  
2. 一个轻量共享 store 管工作台级筛选条件、当前数据集、当前选中池。  
3. 异步任务独立封装，不与视图状态强耦合。

## 9.3 列表性能建议

因为你明确会进入“几十到上百个配色盘”的场景，首版就该考虑：

1. 网格列表虚拟化。  
2. 图片或复杂预览的惰性渲染。  
3. 卡片密度切换。  
4. 预览组件输入标准化，避免每个卡片重复做重计算。

---

## 10. 本地脚本与 CLI 建议

## 10.1 首版建议至少有四个命令

1. `pnpm validate`  
校验 `source/` 数据结构与规则。

2. `pnpm build-bundle`  
生成客户端消费态 bundle。

3. `pnpm preview-data`  
可选，用于检查当前数据摘要。

4. `pnpm sync-mobile`  
第二阶段接入，把 bundle 同步到移动端工程。

## 10.2 第二阶段再加的命令

1. `pnpm pull-assets`  
从 GitHub 资产仓库同步数据。

2. `pnpm publish-assets`  
把变更推回资产仓库。

这样能保证首版先本地闭环，再远端闭环。

---

## 11. 数据模型建议

## 11.1 编辑态与发布态分离

必须明确区分：

1. 编辑态 `source` 数据。  
2. 发布态 `bundle` 数据。

编辑态可以包含：

- `reviewStatus`  
- `curationScore`  
- `reviewNotes`  
- `rejectionReason`  
- `previewVariant`

发布态则只保留客户端真正需要的字段。

## 11.2 推荐实体

首版建议至少有：

- `BaseColor`  
- `Palette`  
- `Collection`  
- `SceneDefinition`  
- `Manifest`

其中 `Palette` 是策展工作台的核心实体。

## 11.3 `Palette` 编辑态建议结构

```ts
type PaletteReviewStatus =
  | 'candidate'
  | 'shortlisted'
  | 'approved'
  | 'rejected'
  | 'archived'

interface PaletteSourceRecord {
  id: string
  slug: string
  nameZh: string
  nameEn?: string
  primaryColorId: string
  secondaryColorId: string
  accentColorId: string
  primaryScene: string
  sceneTags?: string[]
  styleTags?: string[]
  moodTags?: string[]
  seasonTags?: string[]
  safetyLevel: 'safe' | 'expressive' | 'experimental'
  reviewStatus: PaletteReviewStatus
  curationScore?: number
  isSelected?: boolean
  reviewNotes?: string
  rejectionReason?: string
  previewVariant?: 'today-hero' | 'list-card' | 'detail-atmosphere'
  updatedAt?: string
}
```

这里的重点是：

- 保持编辑态足够表达策展信息。  
- 不强迫移动端消费所有运营字段。

---

## 12. 校验层设计

## 12.1 首版至少需要三类校验

1. 结构校验。  
字段类型、必填项、枚举合法性。

2. 引用校验。  
`primaryColorId`、`secondaryColorId`、`accentColorId` 是否存在。

3. 策展规则校验。  
例如：

- `reviewStatus = rejected` 时必须有 `rejectionReason`。  
- `approved` 候选建议有评分。  
- `isSelected = true` 时不应为 `rejected`。

## 12.2 校验实现建议

建议使用两层：

1. Schema 层。  
负责字段结构合法性。

2. Rule 层。  
负责跨字段和跨实体规则。

这样后续规则扩展更稳定。

---

## 13. 预览层设计

## 13.1 预览层不是读 JSON 后简单渲染色块

预览层的目标是尽可能模拟产品中的真实感受，因此建议把预览实现为一组明确模板，而不是一套随意拼装的色块组件。

## 13.2 推荐预览模板组件

建议至少实现：

1. `TodayHeroPreview`  
模拟今日页主卡。

2. `ListCardPreview`  
模拟列表缩略卡片。

3. `DetailAtmospherePreview`  
模拟详情氛围页。

4. `ContrastCheckPreview`  
专门用于对比文本与背景可读性。

## 9.3 预览层输入原则

预览组件应尽量只依赖一个标准化的 `PalettePreviewModel`，避免各页面各自拼数据。

例如：

```ts
interface PalettePreviewModel {
  name: string
  primaryHex: string
  secondaryHex: string
  accentHex: string
  title: string
  subtitle?: string
  sceneLabel?: string
  tags?: string[]
  status?: string
}
```

这样总览页、对比页、详情页可以复用同一套预览组件。

---

## 14. 读取与保存策略

## 14.1 首版推荐策略

首版建议优先支持以下两种工作模式：

1. 读取本地资产工作区。  
2. 读取从 GitHub 资产仓库同步下来的本地副本。

不建议首版前端直接把 GitHub API 集成到每个页面交互里。

## 14.2 保存策略

建议区分三类保存：

1. 页面内即时保存 UI 状态。  
2. 编辑态数据保存到本地工作区。  
3. 发布动作才触发 bundle 构建和仓库提交。

这样能避免每次筛选动作都和远端同步耦合在一起。

---

## 15. 与 GitHub 资产仓库的衔接

## 15.1 推荐首版实现

建议先通过脚本或服务模块处理：

1. `pull-assets`  
把远端资产仓库同步到本地工作区。

2. `validate-assets`  
执行完整校验。

3. `build-bundle`  
生成移动端消费文件。

4. `publish-assets`  
提交变更到资产仓库。

前端工作台只调用这些能力，而不自己内嵌全部发布逻辑。

## 15.2 后续桌面化时的迁移方式

如果后续封装为桌面端，可以把这些脚本能力迁移为应用内部服务：

- UI 层不变。  
- 服务层从命令行脚本切换为桌面端内部模块。  
- 这样迁移成本最小。

---

## 16. 与移动端工程的衔接

## 16.1 同步原则

移动端项目仍然只消费 `bundle/current/`，不读取工作台编辑态数据。

## 16.2 推荐同步动作

移动端工程只需要一条明确脚本：

1. 从资产仓库获取最新 `bundle/current/`。  
2. 同步到 Harmony 工程的资源目录。  
3. 执行一次基础完整性检查。  
4. 由用户继续在 IDE 中构建和验收。

这能避免把运营工作台逻辑带入客户端工程。

---

## 17. 分阶段实施建议

## 17.1 阶段一：新开工程并跑通本地数据

目标：

- 新开 `daypalette-palette-workbench` 工程。  
- 用 `sample-data/` 跑通总览、预览、对比三页。  
- 先不接 GitHub。  

## 17.2 阶段二：接入真实 source 数据

目标：

- 定义 `source/` 数据格式。  
- 接入真实资产数据。  
- 跑通状态保存与本地校验。  

## 17.3 阶段三：补 bundle 构建

目标：

- 从编辑态构建移动端消费态 bundle。  
- 增加发布前检查页。  

## 17.4 阶段四：接入资产仓库与移动端同步

目标：

- 接入 `pull-assets` 与 `publish-assets`。  
- 补 `sync-mobile` 脚本。  
- 把工作台、资产仓库、App 仓库链路串起来。

---

## 18. 首版实现优先级

## 18.1 P0

1. 总览页。  
2. 批量预览页。  
3. 对比页。  
4. 基础运营字段读写。  
5. 本地数据加载。  
6. 基础校验。  

## 18.2 P1

1. 详情页。  
2. 发布前检查页。  
3. bundle 构建脚本。  
4. 本地导出。

## 18.3 P2

1. GitHub 提交能力。  
2. 原始数据编辑器入口。  
3. 桌面端封装。  
4. 更复杂的相似项推荐与批量编辑。

---

## 19. 风险与控制点

## 19.1 最大风险

最大风险不是实现不了，而是过早做成一个“什么都管”的大工具。

## 19.2 控制点

建议始终控制这几个边界：

1. 工作台优先解决预览与策展，不优先解决全部编辑问题。  
2. 编辑态与发布态必须分离。  
3. UI 层与资产处理服务必须分层。  
4. 远端同步不应耦合到每一次本地操作。  
5. 移动端继续只消费 bundle。

---

## 20. 最终建议

如果只给一个技术实现建议，那就是：

**先做一个本地 Web 策展工作台，把预览模板、筛选流程、运营状态和校验脚本跑通；把桌面端能力视作第二阶段封装，而不是首版前提。**

这样做的收益是：

1. 能最快验证真实工作流。  
2. 不会过早背上桌面端复杂度。  
3. 未来仍然可以平滑升级到桌面端工作台。  
