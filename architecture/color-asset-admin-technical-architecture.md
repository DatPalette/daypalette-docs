# 配色资产管理后台技术设计

> 状态：草案 v0.1（2026-04-29）
> 对应产品文档：[`../product/color-asset-admin-prd.md`](../product/color-asset-admin-prd.md)
> 数据合同草案：[`./color-asset-admin-data-contract.md`](./color-asset-admin-data-contract.md)
> 设计真相源：[`../design/shared-design-language.md`](../design/shared-design-language.md)
> 背景说明：旧 `daypalette-color-assets` / `daypalette-palette-workbench` 方案已归档，见 [`../archive/color-operations-reset-2026-04.md`](../archive/color-operations-reset-2026-04.md)

## 1. 文档定位

本文用于定义“配色资产管理后台”首版的共享层技术方案。

本文重点回答：

1. 新方案采用什么仓库边界。
2. 哪些文件是真相源，哪些仓库只负责工具。
3. monorepo 内前端、服务端、共享包如何拆分。
4. 基础数据怎样从“散落字符串”升级成“可单独管理的数据字典”。
5. 文件读写、校验、删除约束和 API 应如何设计。

---

## 2. 一句话结论

DayPalette 新一轮配色运营应采用一个**独立的本地 monorepo 工程**，其中 `NestJS` 服务负责读取、校验并写回 `day_palette` 中的原始 JSON 文件，`Vite + shadcn/ui + Tailwind CSS` 前端负责管理界面，所有删除动作都按软删除处理，且后台视觉实现以 [`../design/shared-design-language.md`](../design/shared-design-language.md) 为设计真相源；`day_palette` 继续作为当前配色原始文件真相源，而不是把原始数据再拆到新的独立资产仓库中。

---

## 3. 仓库边界与真相源

### 3.1 仓库角色

新方案建议只保留三类仓库角色：

1. `day_palette`
   - 保存 App 工程。
   - 保存当前配色原始文件。
   - 继续作为 HarmonyOS 运行时资源落点。

2. 新的后台 monorepo
   - 保存后台前端与服务端代码。
   - 通过本地文件访问 `day_palette` 中的原始 JSON。
   - 不把配色数据复制成新的长期真相源。

3. `daypalette-docs`
   - 保存共享 PRD、架构与协作原则。

### 3.2 真相源表

| 主题 | 真相源位置 | 说明 |
| --- | --- | --- |
| 共享产品与架构判断 | `daypalette-docs/` | 共享层先写这里 |
| Base Colors 原始数据 | `day_palette/entry/src/main/resources/rawfile/palette-data/base-colors.v1.json` | 当前主数据文件 |
| Palettes 原始数据 | `day_palette/entry/src/main/resources/rawfile/palette-data/palettes.v1.json` | 当前主数据文件 |
| Collections 原始数据 | `day_palette/entry/src/main/resources/rawfile/palette-data/collections.v1.json` | 当前主数据文件 |
| 基础数据字典 | `day_palette/entry/src/main/resources/rawfile/palette-data/dictionaries.v1.json` | 新增文件，承载可枚举口径 |
| 后台实现代码 | 新 monorepo | 工具层，不承载业务真相源 |

### 3.3 关键原则

1. `day_palette` 继续保存原始文件，这是当前方案的核心前提。
2. 新 monorepo 只负责读写、校验、管理和界面操作，不自建长期数据库真相源。
3. 后台提供的是本地管理 API，不是面向 App 运行时的线上服务。
4. 如果未来要改成数据库或远端服务，应另起共享文档，而不是在首版里偷渡复杂度。

---

## 4. monorepo 结构建议

建议使用 `pnpm workspace`，保持轻量，不引入额外 monorepo 框架。

推荐结构：

```text
daypalette-color-admin/
  apps/
    admin-web/
    admin-api/
  packages/
    contracts/
    file-store/
    validation/
  package.json
  pnpm-workspace.yaml
```

各部分职责：

### 4.1 `apps/admin-web`

职责：

1. 提供左侧菜单右侧内容页的后台界面。
2. 展示基础数据页面与三类主数据页面。
3. 处理搜索、筛选、列表、详情表单和删除确认。

推荐技术：

1. React + TypeScript
2. Vite
3. shadcn/ui
4. Tailwind CSS

### 4.2 `apps/admin-api`

职责：

1. 读取 `day_palette` 文件。
2. 执行数据校验、引用分析和写回。
3. 向前端提供本地 REST API。

推荐技术：

1. NestJS
2. TypeScript
3. Node.js 文件系统 API

### 4.3 `packages/contracts`

职责：

1. 维护前后端共享类型。
2. 维护 DTO、枚举键名和基础 schema。
3. 保证前后端对字段名与数据结构的理解一致。

### 4.4 `packages/file-store`

职责：

1. 提供读取 / 写入 JSON 文件的统一封装。
2. 封装文件路径解析、原子写入和版本戳生成。
3. 隔离 `day_palette` 路径配置，不让业务层散落硬编码路径。

### 4.5 `packages/validation`

职责：

1. 校验主数据结构。
2. 校验字典结构。
3. 校验跨文件引用与删除约束。

---

## 5. 数据设计

### 5.1 当前主数据文件

当前首版直接围绕以下文件工作：

1. `base-colors.v1.json`
2. `palettes.v1.json`
3. `collections.v1.json`

这三份文件仍然保留现有 `version`、`updatedAt`、`items` 顶层结构。

### 5.2 新增基础数据字典文件

为了让散落在各记录内部的可枚举值可以被单独管理，建议新增：

1. `dictionaries.v1.json`

推荐结构：

```json
{
  "version": 1,
  "updatedAt": "2026-04-29T00:00:00Z",
  "dictionaries": {
    "occasion": {
         "key": "occasion",
         "labelZh": "场合",
         "labelEn": "Occasion",
      "entityScopes": ["baseColor", "palette", "collection"],
         "selectionMode": "mixed",
         "fieldMappings": [
            {
               "entity": "baseColor",
               "field": "occasionTags",
               "selectionMode": "multi"
            },
            {
               "entity": "palette",
               "field": "occasionId",
               "selectionMode": "single"
            }
         ],
      "items": [
        {
          "id": "workday",
          "labelZh": "工作日",
          "labelEn": "Workday",
          "sortOrder": 100,
               "isActive": true,
               "isDeleted": false,
               "deletedAt": "",
               "deleteReason": ""
        }
      ]
    }
  }
}
```

设计原则：

1. 主数据文件里继续只保存稳定 ID，如 `workday`、`minimal`、`safe`。
2. 展示名、排序、启用状态、删除状态收口到 `dictionaries.v1.json`。
3. 单选、多选通过 `selectionMode` 与 `fieldMappings` 描述，而不是在前端硬编码。
4. 同一口径如果跨多个实体复用，只维护一份字典。
5. 详细保留字段见 [`./color-asset-admin-data-contract.md`](./color-asset-admin-data-contract.md)。

### 5.3 首版字典键建议

首版至少定义这些字典键：

1. `occasion`
2. `styleTag`
3. `seasonTag`
4. `moodTag`
5. `colorFamily`
6. `tone`
7. `lightnessLevel`
8. `saturationLevel`
9. `safetyLevel`
10. `themeType`
11. `releaseMode`
12. `sourceType`
13. `status`

### 5.4 字段映射关系

| 数据文件字段 | 字典键 | 选择模式 |
| --- | --- | --- |
| `baseColor.tone` | `tone` | `single` |
| `baseColor.lightnessLevel` | `lightnessLevel` | `single` |
| `baseColor.saturationLevel` | `saturationLevel` | `single` |
| `baseColor.colorFamily` | `colorFamily` | `single` |
| `baseColor.styleTags[]` | `styleTag` | `multi` |
| `baseColor.occasionTags[]` | `occasion` | `multi` |
| `baseColor.seasonTags[]` | `seasonTag` | `multi` |
| `baseColor.status` | `status` | `single` |
| `palette.occasionId` | `occasion` | `single` |
| `palette.moodTags[]` | `moodTag` | `multi` |
| `palette.styleTags[]` | `styleTag` | `multi` |
| `palette.seasonTags[]` | `seasonTag` | `multi` |
| `palette.safetyLevel` | `safetyLevel` | `single` |
| `palette.sourceType` | `sourceType` | `single` |
| `palette.status` | `status` | `single` |
| `collection.themeType` | `themeType` | `single` |
| `collection.occasionTags[]` | `occasion` | `multi` |
| `collection.styleTags[]` | `styleTag` | `multi` |
| `collection.releaseMode` | `releaseMode` | `single` |
| `collection.status` | `status` | `single` |

### 5.5 删除策略

不同对象的删除策略如下：

1. 所有删除动作默认都是软删除，不物理移除 JSON 记录。
2. 字典项：删除前先做引用检查；通过后写入软删除标记，并从默认选项列表隐藏。
3. Base Color：删除前先检查 palette 引用；通过后写入软删除标记。
4. Palette：删除前先检查 collection 引用；通过后写入软删除标记。
5. Collection：删除前仍做一致性检查；通过后写入软删除标记。

补充约束：

1. 主数据的软删除推荐统一落到系统保留状态值 `deleted`，不得物理删行。
2. 字典数据的软删除应与“停用”区分开，避免把“暂时停用”和“已删除”混成同一种状态。
3. 默认查询不返回软删除记录，只有显式带筛选条件时才返回。

### 5.6 详细字段合同

当前阶段字段合同进一步约定为：

1. 主数据软删除统一写为 `status=deleted`，并附带 `previousStatus`、`deletedAt`、`deleteReason`。
2. 字典项通过 `isActive` 与 `isDeleted` 两个字段区分“停用”和“已删除”。
3. `dictionaries.v1.json` 的完整草案结构与保留字段见 [`./color-asset-admin-data-contract.md`](./color-asset-admin-data-contract.md)。

---

## 6. 服务端设计

### 6.1 模块拆分

NestJS 首版建议拆成以下模块：

1. `ConfigModule`
   - 解析 `day_palette` 目标路径。

2. `DictionariesModule`
   - 管理 `dictionaries.v1.json`。

3. `BaseColorsModule`
   - 管理 `base-colors.v1.json`。

4. `PalettesModule`
   - 管理 `palettes.v1.json`。

5. `CollectionsModule`
   - 管理 `collections.v1.json`。

6. `ValidationModule`
   - 聚合所有校验与引用分析。

### 6.2 API 形态

首版建议采用 REST，接口保持朴素：

1. `GET /api/dictionaries`
2. `GET /api/dictionaries/:key`
3. `POST /api/dictionaries/:key/items`
4. `PUT /api/dictionaries/:key/items/:id`
5. `DELETE /api/dictionaries/:key/items/:id`
6. `GET /api/base-colors`
7. `POST /api/base-colors`
8. `PUT /api/base-colors/:id`
9. `DELETE /api/base-colors/:id`
10. `GET /api/palettes`
11. `POST /api/palettes`
12. `PUT /api/palettes/:id`
13. `DELETE /api/palettes/:id`
14. `GET /api/collections`
15. `POST /api/collections`
16. `PUT /api/collections/:id`
17. `DELETE /api/collections/:id`
18. `POST /api/validate`

补充说明：

1. 以上 `DELETE` 接口统一执行逻辑删除，不物理移除条目。
2. 所有 `DELETE` 接口在写入软删除标记前都必须先通过引用检查。

### 6.3 读写流程

每次写入建议遵守以下流程：

1. 读取目标文件当前内容。
2. 结合其他关联文件执行结构校验与引用校验。
3. 生成新的 `updatedAt` 与版本戳。
4. 采用临时文件 + rename 的原子写入方式落盘。
5. 返回最新数据与版本戳给前端。

### 6.4 并发保护

虽然当前是单人本地使用，仍建议加上最小并发保护，避免多标签页覆盖。

推荐做法：

1. 每个资源列表与详情响应都附带 `revision`。
2. 更新请求必须带上 `revision`。
3. 服务端发现文件已变化时返回 `409 Conflict`。

---

## 7. 前端设计

### 7.1 页面结构

前端采用固定壳层：

1. `AppShell`
   - 左侧菜单
   - 顶部面包屑 / 页面标题
   - 右侧内容区

2. `ResourcePage`
   - 筛选条
   - 主列表 / 网格
   - 详情抽屉 / 弹层

3. `ConfirmDialog`
   - 删除确认
   - 引用冲突提示

### 7.2 资源页模式

首版只保留三类资源页模式：

1. 字典页：表格 + 详情抽屉。
2. 基础色页：网格卡片 + 详情抽屉。
3. 配色 / 合集页：列表或网格 + 详情抽屉。

### 7.3 表单策略

表单建议统一由 schema 驱动：

1. 单选字段从字典接口读取选项。
2. 多选字段从字典接口读取选项。
3. 布尔字段使用开关或复选框。
4. 颜色字段输入时即时预览。

### 7.4 视觉约束

虽然是后台，但视觉实现仍必须以 [`../design/shared-design-language.md`](../design/shared-design-language.md) 为设计真相源：

1. 页面背景基于 `bg.page`，主卡片基于 `surface.card`。
2. 标题与正文遵守共享文档里的 Display / UI 字体角色分工。
3. 边框、阴影、留白和毛玻璃条遵守共享文档里的材质与低噪声原则。
4. 颜色与配色展示优先采用低噪声卡片，而不是重表格。
5. 不使用重后台模板风格，也不单独发明与共享语言冲突的视觉口径。

---

## 8. 校验规则

首版至少执行以下校验：

1. JSON 结构合法。
2. 各文件内 `id` 唯一。
3. `palette.primaryColorId / secondaryColorId / accentColorId` 必须都能找到对应 Base Color。
4. `collection.paletteIds` 与 `coverPaletteId` 必须都能找到对应 Palette。
5. 主数据里出现的字典值必须存在于 `dictionaries.v1.json`。
6. 软删除前必须先通过引用检查。
7. `hex` 必须是合法颜色值。
8. 关键必填字段不能为空。

建议把校验结果统一格式化为：

1. 错误码
2. 错误消息
3. 资源类型
4. 资源 ID
5. 字段路径

---

## 9. 迁移建议

因为当前 `day_palette` 里还没有 `dictionaries.v1.json`，首版落地前建议先走一轮迁移：

1. 从现有三份主数据里扫描所有枚举型值。
2. 生成首版 `dictionaries.v1.json` 草稿。
3. 人工清洗重复、别名和无效值。
4. 后台正式切换为“所有枚举字段只认字典文件中的合法值”。

这一步的意义是把“字符串散点事实”收口成“可管理的基础数据层”。

---

## 10. 非目标与延后项

首版技术设计明确不做：

1. 数据库持久化。
2. 登录鉴权。
3. 实时协同。
4. 文件监听自动热同步到 App。
5. 二进制资源上传系统。
6. 大而全的低代码后台框架。

---

## 11. 风险与取舍

### 11.1 直接读写 `day_palette` 文件的风险

风险：

1. 后台工程与 App 工程存在路径耦合。
2. 错误写入会直接污染当前原始文件。

取舍：

1. 当前这是更符合“简单高效”的方案。
2. 通过原子写入、版本戳和校验来控制风险。

### 11.2 新增字典文件的兼容风险

风险：

1. 现有主数据此前并不依赖独立字典文件。

取舍：

1. App 仍然继续消费原三份主数据，不会直接被新文件破坏。
2. 字典文件主要服务后台维护效率，是首版最值得新增的结构化层。

### 11.3 本地服务而不是数据库服务的限制

风险：

1. 不适合多人同时远端协作。
2. 后续若要扩成线上系统，需要再做一次架构升级。

取舍：

1. 当前目标是先把单人维护效率做对。
2. 等真实运营负担出现，再决定是否升级为数据库方案。

---

## 12. 实施顺序建议

建议按以下顺序推进：

1. 先定义并生成 `dictionaries.v1.json`。
2. 再完成 `contracts` 与 `validation` 共享包。
3. 再实现 NestJS 读写 API。
4. 最后实现前端页面与交互。

这个顺序能先把数据合同和删除约束钉住，避免先画界面再返工数据层。