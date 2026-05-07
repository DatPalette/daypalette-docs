# 配色资产后台数据合同草案

> 状态：草案 v0.1（2026-04-29）
> 上游产品文档：[`../product/color-asset-admin-prd.md`](../product/color-asset-admin-prd.md)
> 上游技术文档：[`./color-asset-admin-technical-architecture.md`](./color-asset-admin-technical-architecture.md)
> 设计真相源：[`../design/shared-design-language.md`](../design/shared-design-language.md)

## 1. 文档定位

本文用于把“配色资产管理后台”里已经决定的数据合同单独收口，避免软删除语义、字典结构和保留字段散落在多份文档里。

本文重点回答：

1. 主数据的软删除到底怎么写。
2. `status=deleted` 适用于哪些对象。
3. 字典项如何区分“停用”与“已删除”。
4. `dictionaries.v1.json` 的首版草案结构和保留字段是什么。

---

## 2. 主数据软删除合同

### 2.1 适用范围

主数据指以下三份文件中的 `items[]` 记录：

1. `base-colors.v1.json`
2. `palettes.v1.json`
3. `collections.v1.json`

### 2.2 生命周期状态合同

主数据对象继续沿用各自原有生命周期状态，但统一追加一个系统保留值 `deleted`，用于软删除。

| 实体 | 业务状态集合 | 软删除保留值 |
| --- | --- | --- |
| Base Color | `draft` / `approved` / `archived` | `deleted` |
| Palette | `draft` / `approved` / `published` / `archived` | `deleted` |
| Collection | `planning` / `ready` / `published` / `archived` | `deleted` |

约束：

1. `deleted` 只代表后台逻辑删除，不代表业务态归档。
2. `archived` 与 `deleted` 不能混用。
3. 后台默认列表不返回 `status=deleted` 的记录，除非显式查看“已删除”。

### 2.3 主数据保留字段

对三类主数据，首版约定以下软删除保留字段：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `status` | string | 生命周期字段；软删除时写成 `deleted` |
| `previousStatus` | string | 软删除前的状态；恢复时回写此值 |
| `deletedAt` | string | ISO 时间戳；软删除时必填 |
| `deleteReason` | string | 可选；记录删除原因 |

写入规则：

1. 执行软删除时，必须先通过引用检查。
2. 软删除成功后，写入 `status=deleted`。
3. 软删除成功后，必须保留 `previousStatus`，用于未来恢复。
4. 软删除成功后，必须写入 `deletedAt`。
5. 恢复记录时，先校验引用和数据结构，再把 `status` 恢复为 `previousStatus`，并清空 `deletedAt`、`deleteReason`、`previousStatus`。

### 2.4 主数据软删除示例

```json
{
  "id": "w1",
  "slug": "morning-mist",
  "primaryColorId": "bc_0039",
  "secondaryColorId": "bc_0031",
  "accentColorId": "bc_0014",
  "occasionId": "workday",
  "moodTags": ["calm", "gentle"],
  "styleTags": ["commute", "urban"],
  "seasonTags": ["spring", "autumn"],
  "safetyLevel": "safe",
  "fitPhotoScenario": false,
  "sourceType": "curated",
  "sourceCollectionIds": ["col_0001"],
  "isPro": false,
  "status": "deleted",
  "previousStatus": "published",
  "deletedAt": "2026-04-29T10:30:00Z",
  "deleteReason": "与合集策略重复，转入历史记录"
}
```

---

## 3. 字典项状态合同

### 3.1 核心原则

字典项的“停用”与“已删除”必须是两个不同语义：

1. 停用：暂时不再给新记录选择，但仍是合法历史值。
2. 已删除：已经执行软删除，默认不再返回，也不应再被任何主数据引用。

### 3.2 字典项保留字段

字典项首版约定以下状态字段：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `isActive` | boolean | 是否可在新建 / 编辑表单中被正常选择 |
| `isDeleted` | boolean | 是否已经执行软删除 |
| `deletedAt` | string | ISO 时间戳；软删除时必填 |
| `deleteReason` | string | 可选；记录删除原因 |

状态判定规则：

| 语义 | `isActive` | `isDeleted` | 默认选择器可见 | 默认列表可见 |
| --- | --- | --- | --- | --- |
| 正常可用 | `true` | `false` | 是 | 是 |
| 已停用 | `false` | `false` | 否 | 是 |
| 已删除 | `false` | `true` | 否 | 否 |

补充约束：

1. `isDeleted=true` 时，`isActive` 必须同时为 `false`。
2. `isDeleted=true` 时，必须先通过引用检查。
3. 被主数据引用的字典项只能停用，不能软删除。
4. 停用项仍允许作为历史记录值显示，但不应出现在新建表单默认选项中。

---

## 4. `dictionaries.v1.json` 顶层合同

### 4.1 顶层结构

```json
{
  "version": 1,
  "updatedAt": "2026-04-29T00:00:00Z",
  "dictionaries": {}
}
```

顶层字段：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `version` | number | 文件结构版本 |
| `updatedAt` | string | 最近更新时间 |
| `dictionaries` | object | 所有字典节点，key 为字典键名 |

### 4.2 字典节点保留字段

每个字典节点建议至少包含：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `key` | string | 字典键名，如 `occasion` |
| `labelZh` | string | 后台菜单或表单里的中文名称 |
| `labelEn` | string | 英文名称 |
| `descriptionZh` | string | 中文说明，可为空 |
| `descriptionEn` | string | 英文说明，可为空 |
| `entityScopes` | string[] | 涉及的实体范围，如 `baseColor`、`palette`、`collection` |
| `selectionMode` | string | `single` / `multi` / `mixed` |
| `fieldMappings` | array | 记录这个字典被哪些字段消费 |
| `items` | array | 字典项列表 |

### 4.3 字典项保留字段

每个字典项建议至少包含：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `id` | string | 稳定 ID，主数据只保存这个值 |
| `labelZh` | string | 中文展示名 |
| `labelEn` | string | 英文展示名 |
| `aliases` | string[] | 可选别名，用于迁移与搜索 |
| `descriptionZh` | string | 中文说明，可为空 |
| `descriptionEn` | string | 英文说明，可为空 |
| `sortOrder` | number | 排序值 |
| `isActive` | boolean | 是否仍对新数据开放 |
| `isDeleted` | boolean | 是否已软删除 |
| `deletedAt` | string | ISO 时间戳；未删除可为空 |
| `deleteReason` | string | 可选；删除原因 |
| `appliesTo` | string[] | 可选；用于限定某些项只适用于特定字段 |

---

## 5. 首版字典键清单

首版建议保留以下字典键：

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

其中：

1. `occasion` 必须支持 `single` 与 `multi` 两种消费方式，因此其 `selectionMode` 应为 `mixed`。
2. `status` 也应为 `mixed`，因为不同实体的合法状态集合不同。
3. `status` 字典中的部分项应通过 `appliesTo` 限定作用域。

---

## 6. `dictionaries.v1.json` 草案示例

```json
{
  "version": 1,
  "updatedAt": "2026-04-29T00:00:00Z",
  "dictionaries": {
    "occasion": {
      "key": "occasion",
      "labelZh": "场合",
      "labelEn": "Occasion",
      "descriptionZh": "跨基础色、配色盘和合集复用的场合口径。",
      "descriptionEn": "Shared occasion vocabulary across base colors, palettes, and collections.",
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
        },
        {
          "entity": "collection",
          "field": "occasionTags",
          "selectionMode": "multi"
        }
      ],
      "items": [
        {
          "id": "workday",
          "labelZh": "工作日",
          "labelEn": "Workday",
          "aliases": [],
          "descriptionZh": "通勤与正式日常场景。",
          "descriptionEn": "Commute and formal everyday scenarios.",
          "sortOrder": 100,
          "isActive": true,
          "isDeleted": false,
          "deletedAt": "",
          "deleteReason": ""
        },
        {
          "id": "weekend",
          "labelZh": "周末",
          "labelEn": "Weekend",
          "aliases": [],
          "descriptionZh": "周末、约会与轻松出门场景。",
          "descriptionEn": "Weekend, date, and casual outing scenarios.",
          "sortOrder": 200,
          "isActive": true,
          "isDeleted": false,
          "deletedAt": "",
          "deleteReason": ""
        }
      ]
    },
    "status": {
      "key": "status",
      "labelZh": "状态",
      "labelEn": "Status",
      "descriptionZh": "主数据生命周期状态字典。",
      "descriptionEn": "Lifecycle status vocabulary for primary resources.",
      "entityScopes": ["baseColor", "palette", "collection"],
      "selectionMode": "mixed",
      "fieldMappings": [
        {
          "entity": "baseColor",
          "field": "status",
          "selectionMode": "single"
        },
        {
          "entity": "palette",
          "field": "status",
          "selectionMode": "single"
        },
        {
          "entity": "collection",
          "field": "status",
          "selectionMode": "single"
        }
      ],
      "items": [
        {
          "id": "approved",
          "labelZh": "已通过",
          "labelEn": "Approved",
          "aliases": [],
          "descriptionZh": "已确认可用，但不一定已发布。",
          "descriptionEn": "Validated and ready, but not necessarily published.",
          "sortOrder": 200,
          "isActive": true,
          "isDeleted": false,
          "deletedAt": "",
          "deleteReason": "",
          "appliesTo": ["baseColor.status", "palette.status"]
        },
        {
          "id": "published",
          "labelZh": "已发布",
          "labelEn": "Published",
          "aliases": [],
          "descriptionZh": "已对消费端生效。",
          "descriptionEn": "Published to the consumer-facing dataset.",
          "sortOrder": 300,
          "isActive": true,
          "isDeleted": false,
          "deletedAt": "",
          "deleteReason": "",
          "appliesTo": ["palette.status", "collection.status"]
        },
        {
          "id": "deleted",
          "labelZh": "已删除",
          "labelEn": "Deleted",
          "aliases": [],
          "descriptionZh": "系统保留的软删除状态。",
          "descriptionEn": "System-reserved soft delete state.",
          "sortOrder": 9999,
          "isActive": false,
          "isDeleted": false,
          "deletedAt": "",
          "deleteReason": "",
          "appliesTo": ["baseColor.status", "palette.status", "collection.status"]
        }
      ]
    }
  }
}
```

---

## 7. 查询与写回行为

## 7. Palette 溯源与审核字段合同

### 7.1 目标

为了支撑“市场参考驱动的配色资产运营”，`palette` 首版需要补齐一组明确的后台运营元数据字段，避免以下问题：

1. 只知道最终 palette，不知道它参考了哪些公开市场样本。
2. 只知道结果被发布，不知道它是否经过人工审核。
3. 首轮 160 个重建和后续月更无法按批次追踪。
4. 旧 palette 转入 `archived` 时缺少结构化原因和时间戳。

### 7.2 适用范围

本节字段仅适用于：

1. `palettes.v1.json`

这些字段属于后台运营元数据，允许出现在 `palette` 原始记录中；消费端若当前不使用这些字段，应保持前向兼容并忽略未知字段。

### 7.3 字段清单

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `referenceMethod` | string | 参考方法；建议值：`market-sampled` / `editorial-derived` / `internal-recomposition` |
| `referenceSources` | array | 参考来源列表；记录公开市场样本 |
| `marketSignalSummary` | string | 对本 palette 提炼自哪些市场趋势的摘要 |
| `productionBatchId` | string | 生产批次，例如 `phase1-workday-v1`、`2026-06-refresh` |
| `reviewStatus` | string | 审核状态；建议值：`pending` / `needsRevision` / `approved` / `rejected` |
| `reviewedAt` | string | 审核时间；未审核可为空 |
| `reviewer` | string | 审核人；未审核可为空 |
| `reviewNotes` | string | 审核备注；未审核可为空 |
| `archivedAt` | string | 归档时间；仅 `status=archived` 时填写 |
| `archiveReason` | string | 归档原因；仅 `status=archived` 时建议填写 |

补充说明：

1. 既有 `sourceType` 继续表达“人工策展 / 重组 / 生成”等高层来源语义。
2. `referenceMethod` 不替代 `sourceType`，而是补充说明该条 palette 的研究与采样方法。

### 7.4 `referenceSources[]` 子结构

`referenceSources[]` 的每个元素建议至少包含：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `sourceId` | string | 后台内部参考记录 ID |
| `platform` | string | 平台名称，例如品牌官网、旗舰店平台或多品牌电商 |
| `channelType` | string | 渠道类型；建议值：`brand-site` / `brand-flagship-store` / `multi-brand-platform` / `marketplace-brand-store` |
| `brandName` | string | 品牌名 |
| `sourceUrl` | string | 公开可访问链接 |
| `observedAt` | string | 采样时间 |
| `itemCategory` | string | 服饰品类，例如衬衫、外套、针织、半裙 |
| `colorSummary` | string[] | 从样本中提取出的 3 到 5 个主色摘要 |
| `notes` | string | 内部分析说明 |

### 7.5 写入规则

1. `status=approved` 或 `status=published` 的 palette，必须满足 `reviewStatus=approved`。
2. `referenceMethod=market-sampled` 且 `status=approved` 或 `status=published` 时，`referenceSources.length` 至少为 3。
3. 上述 3 条参考记录中，至少应包含 2 个不同 `platform` 值。
4. 上述 3 条参考记录中，至少应包含 2 个不同 `brandName` 值。
5. `status=archived` 时，必须写入 `archivedAt`；`archiveReason` 为强烈建议字段。
6. `referenceSources` 只保存结构化链接和内部说明，不保存截图二进制或外部营销文案正文。

### 7.6 `palette` 记录示例

```json
{
  "id": "w101",
  "slug": "soft-commute-shell",
  "primaryColorId": "bc_0101",
  "secondaryColorId": "bc_0045",
  "accentColorId": "bc_0070",
  "occasionId": "workday",
  "moodTags": ["calm", "polished"],
  "styleTags": ["commute", "urban"],
  "seasonTags": ["spring", "autumn"],
  "safetyLevel": "safe",
  "fitPhotoScenario": false,
  "sourceType": "curated",
  "referenceMethod": "market-sampled",
  "referenceSources": [
    {
      "sourceId": "ref_workday_001",
      "platform": "brand-site",
      "channelType": "brand-site",
      "brandName": "Example Brand A",
      "sourceUrl": "https://example.com/look-1",
      "observedAt": "2026-05-07T09:00:00Z",
      "itemCategory": "shirt",
      "colorSummary": ["shell white", "soft taupe", "mist blue"],
      "notes": "轻正式衬衫 + 灰蓝下装关系稳定。"
    },
    {
      "sourceId": "ref_workday_014",
      "platform": "official-flagship",
      "channelType": "brand-flagship-store",
      "brandName": "Example Brand B",
      "sourceUrl": "https://example.com/look-2",
      "observedAt": "2026-05-07T09:10:00Z",
      "itemCategory": "knitwear",
      "colorSummary": ["oat beige", "smoke grey", "soft plum"],
      "notes": "强调柔和提气色，不做高对比。"
    },
    {
      "sourceId": "ref_workday_022",
      "platform": "multi-brand-platform",
      "channelType": "multi-brand-platform",
      "brandName": "Example Brand C",
      "sourceUrl": "https://example.com/look-3",
      "observedAt": "2026-05-07T09:20:00Z",
      "itemCategory": "blazer",
      "colorSummary": ["mist blue", "stone grey", "ink navy"],
      "notes": "提供更稳的外套层次。"
    }
  ],
  "marketSignalSummary": "2026 春季通勤样本中，雾蓝 + 石灰 + 柔和莓色点缀反复出现。",
  "productionBatchId": "phase1-workday-v1",
  "reviewStatus": "approved",
  "reviewedAt": "2026-05-07T11:00:00Z",
  "reviewer": "owner",
  "reviewNotes": "保留柔和气色，但控制 accent 面积感。",
  "status": "approved",
  "archivedAt": "",
  "archiveReason": ""
}
```

---

## 8. 查询与写回行为

首版建议遵守以下行为：

1. 资源列表默认不返回主数据 `status=deleted` 的记录。
2. 字典列表默认不返回 `isDeleted=true` 的项。
3. 表单下拉默认只展示 `isActive=true` 且 `isDeleted=false` 的字典项。
4. 历史记录详情页允许显示“已停用但未删除”的字典项。
5. 执行软删除时，若引用检查未通过，服务端必须返回冲突信息并拒绝写回。

---

## 9. 当前阶段结论

当前阶段先把以下三件事钉死即可：

1. 主数据统一用 `status=deleted` 执行软删除。
2. 字典项统一用 `isActive` 与 `isDeleted` 区分“停用”与“已删除”。
3. `dictionaries.v1.json` 先成为可枚举字段的结构化真相源，再继续推进后台实现。
4. `palette` 需要补齐溯源、审核和归档元数据，才能支撑首轮重建和后续月更。