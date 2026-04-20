# 配色资产库结构与低成本维护方案

> 来源：原 `day_palette/docs/product/strategy/color-asset-library-plan.md`，2026-04-20 提升到顶层共享层。
> 本文定义 DayPalette 三层资产结构、字段草表与命名规则，是 `day_palette` / `daypalette-color-assets` / `daypalette-palette-workbench` 共同消费的合同。

## 1. 文档目的

这份文档用于定义 DayPalette 的配色资产应该如何被结构化管理，以及在**重工具、轻内容**方向下，如何以尽可能低的成本维护它。

当前核心判断：

- 配色资产是 DayPalette 的重要内容资产，但不应演变成高成本内容运营项目。
- 前期更重要的是先建立**稳定的数据结构、命名规则、复用机制和审核口径**，而不是急着上一个很重的 CMS。
- 内容维护应坚持：**只做精，不做多；可复用、可重组、可审校。**

---

## 2. 目标与非目标

### 2.1 目标

1. 建立一套可长期复用的配色资产结构。  
2. 让基础单色、配色盘、专题策展三层资产彼此可关联。  
3. 保证新增、重组、审核、发布的成本可控。  
4. 支撑后续专题策展、智能配、场景推荐等能力。

### 2.2 非目标

1. 不追求一开始就做复杂后台系统。  
2. 不做文章型内容 CMS。  
3. 不追求海量内容更新频率。  
4. 不在前期引入过多运营角色和流程。

---

## 3. 资产分层

建议把 DayPalette 的配色资产拆成三层。

### 3.1 第一层：基础单色 Base Color

这是最底层、可复用的原子资产。

建议作用：

- 作为配色盘的组成单元。  
- 作为智能配候选池的来源。  
- 作为专题重组与扩展的基础材料。

建议字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | string | 唯一 ID，例如 `bc_0001` |
| `hex` | string | 十六进制颜色值，例如 `#CBB7A1` |
| `nameZh` | string | 中文名，例如「雾杏」 |
| `nameEn` | string | 英文名，可选 |
| `tone` | enum | 冷 / 暖 / 中性 |
| `lightnessLevel` | enum | 浅 / 中 / 深 |
| `saturationLevel` | enum | 低 / 中 / 高 |
| `colorFamily` | enum | 米白 / 灰 / 蓝 / 绿 / 棕 / 红等 |
| `styleTags` | string[] | 都市、松弛、古风、通勤等 |
| `occasionTags` | string[] | 通勤、约会、citywalk、公园等 |
| `seasonTags` | string[] | 春 / 夏 / 秋 / 冬 |
| `isNeutralCore` | boolean | 是否为高复用中性色 |
| `status` | enum | draft / approved / archived |
| `notes` | string | 主理人备注 |

说明：

- `hex` 是机器标识，不应成为唯一的人类命名方式。  
- 后续做策展、搜索、推荐时，更依赖标签和分类，而不是只靠颜色值。

### 3.2 第二层：配色盘 Palette

这是 DayPalette 直接面向用户输出的核心资产。

建议每个配色盘固定包含：

- `primary`  
- `secondary`  
- `accent`

建议字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | string | 唯一 ID，例如 `pl_0101` |
| `slug` | string | 稳定别名，例如 `mist-city-morning` |
| `nameZh` | string | 中文名称 |
| `nameEn` | string | 英文名称 |
| `primaryColorId` | string | 引用基础单色 ID |
| `secondaryColorId` | string | 引用基础单色 ID |
| `accentColorId` | string | 引用基础单色 ID |
| `occasionId` | string | 主要适配场景 |
| `moodTags` | string[] | 安静、轻松、清爽、显气色等 |
| `styleTags` | string[] | 通勤、citywalk、新中式等 |
| `seasonTags` | string[] | 春 / 夏 / 秋 / 冬 |
| `genderLean` | enum | neutral / female-leaning / male-leaning，可选 |
| `safetyLevel` | enum | safe / expressive / experimental |
| `fitPhotoScenario` | boolean | 是否适合拍照专题 |
| `heroTitleZh` | string | 今日页主文案用标题 |
| `heroSubtitleZh` | string | 副文案，可选 |
| `sourceType` | enum | curated / recomposed / generated |
| `sourceCollectionIds` | string[] | 所属专题 ID |
| `isPro` | boolean | 是否付费内容 |
| `status` | enum | draft / approved / published / archived |
| `reviewer` | string | 审核人 |
| `reviewNotes` | string | 审核意见 |
| `createdAt` | string | 创建时间 |
| `updatedAt` | string | 更新时间 |

说明：

- `sourceType` 用于区分是人工策展、既有资产重组，还是算法生成后经人工审核入库。  
- `safetyLevel` 用于后续智能配和专题推荐时做边界控制。

### 3.3 第三层：专题 / 合集 Collection

这是面向商业化和运营表达的一层，不直接替代配色盘，而是对配色盘做编组。

建议字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | string | 唯一 ID，例如 `col_0001` |
| `nameZh` | string | 专题名称 |
| `nameEn` | string | 英文名称 |
| `themeType` | enum | 场景 / 季节 / 节日 / 风格 / 商业专题 |
| `descriptionZh` | string | 专题说明 |
| `paletteIds` | string[] | 收录的配色盘 ID 列表 |
| `coverPaletteId` | string | 封面配色盘 |
| `occasionTags` | string[] | 主要场景标签 |
| `styleTags` | string[] | 主要风格标签 |
| `isPro` | boolean | 是否为 Pro 专题 |
| `releaseMode` | enum | permanent / seasonal / limited |
| `status` | enum | planning / ready / published / archived |

当前首批专题建议可围绕：

1. `Citywalk 拍照`  
2. `公园出片`  
3. `国风新中式`

---

## 4. 命名与标签规则

### 4.1 基础单色命名

建议原则：

- `hex` 只作为机器值，不作为用户面命名。  
- 基础单色可采用简洁、稳定、可检索的名称。  
- 不追求过度文艺，重点是后续复用时不混乱。

建议格式：

- 中文：`雾杏`、`岩灰`、`松针绿`  
- 英文：`Mist Apricot`、`Stone Grey`、`Pine Green`

### 4.2 配色盘命名

建议原则：

- 配色盘名称承担品牌表达。  
- 可以由 AI 生成候选，但最终以主理人审核定稿。  
- 命名要能表达场景、气质或故事感，而不是只描述颜色本身。

建议避免：

- 过度抽象、无法联想到穿搭场景。  
- 命名风格不统一。  
- 中文和英文语义明显失配。

### 4.3 标签体系

建议标签分四类，避免无限制生长：

1. `occasionTags`：通勤、约会、citywalk、公园、聚会、新中式等。  
2. `moodTags`：松弛、清爽、显气色、安静、明快等。  
3. `styleTags`：都市、自然、古风、极简、轻复古等。  
4. `seasonTags`：春、夏、秋、冬。

建议限制：

- 每个配色盘核心标签不超过 5 个。  
- 新增标签前先检查是否能复用已有标签。  
- 标签必须服务于推荐、筛选、策展或重组，不能只是“看起来更完整”。

---

## 5. 资产关系与复用策略

### 5.1 复用原则

轻内容的核心，不是停止新增，而是把新增建立在复用之上。

建议原则：

1. 优先复用基础单色，而不是频繁新增大量近似颜色。  
2. 新增配色盘优先通过既有基础单色重组完成。  
3. 仅在已有色库无法表达新场景时，才新增基础单色。  
4. 重点维护高复用中性色资产，例如米白、灰、棕、深蓝、黑等。

### 5.2 重组边界

重组不是自由拼接，必须有约束。

建议边界：

1. 高饱和点缀色不应在多数场景中自由滥用。  
2. 强表达色只应出现在明确允许的专题或场景中。  
3. 中性色和基础安全色应优先承担 `secondary` 角色。  
4. `accent` 应更强调点缀，不应轻易抢走 `primary` 的语义中心。

---

## 6. 审核与上新机制

### 6.1 最小审核流

前期不需要复杂流程，建议采用最小闭环：

1. 创建草稿  
2. 自检标签与命名  
3. 主理人审核  
4. 标记 `approved`  
5. 进入发布清单  
6. 随版本或数据包发布

### 6.2 审核口径

每个配色盘至少检查：

1. 是否符合目标场景。  
2. 是否有明显脏色或失衡风险。  
3. `primary / secondary / accent` 的角色是否清晰。  
4. 命名是否统一、是否过度悬浮。  
5. 是否和现有资产过度重复。

### 6.3 上新节奏建议

在“轻内容”前提下，建议采用克制节奏：

1. `月更`：适合小幅补充和专题微更新。  
2. `季更`：适合较完整的季节性策展。  
3. `节日精选`：适合低频但有明确消费和分享场景的上新。

建议结论：

- 前期不追求周更。  
- 优先保证每次上新都足够成体系。

---

## 7. 最小可行维护方案

### 7.1 推荐的前期方案

在当前阶段，更推荐采用：

- **编辑态**：结构化表格  
- **发布态**：仓库中的 JSON / 配置文件

推荐原因：

- 成本低。  
- 不依赖后端。  
- 便于版本管理。  
- 便于和现有本地 JSON 规则体系衔接。

前期可以采用：

1. 本地主表或在线表格维护基础单色与配色盘。  
2. 定期导出为标准 JSON。  
3. 在仓库中做版本化管理与审校。  
4. App 直接读取本地资源。

### 7.2 CMS 选型建议

当前不建议把 CMS 当成前置条件。

建议分阶段看：

1. `阶段 0`：纯表格 + JSON，先跑通结构和流程。  
2. `阶段 1`：如果资产规模增长，再考虑轻量表格型或自托管型工具。  
3. `阶段 2`：只有在多人协作、频繁更新、需要审校权限时，再考虑更完整 CMS。

如果后续要调研免费或低成本方案，可优先看：

1. 表格型：Airtable 免费版思路、Baserow、NocoDB。  
2. 自托管型：Directus、Strapi。  
3. 纯仓库型：Git 管理 JSON + 脚本校验。

但当前阶段最推荐的仍然是：

- **先定义结构，再决定要不要上 CMS。**

---

## 8. 推荐的数据落地顺序

建议按以下顺序推进：

1. 先整理一版基础单色表。  
2. 基于基础单色建立首批高质量配色盘。  
3. 给配色盘补齐场景、风格、情绪标签。  
4. 选出三组专题做首批合集。  
5. 再考虑是否需要半自动导出、校验和 CMS 工具。

---

## 9. 暂定结论

当前阶段更适合 DayPalette 的方案是：

- 基础单色、配色盘、专题合集三层分开管理。  
- 以前期低成本的表格 + JSON 方式维护，而不是急着上 CMS。  
- 配色盘命名由 AI 辅助生成、主理人最终定稿。  
- 内容新增保持克制，重点通过基础单色复用和既有资产重组扩展内容库。  
- 所有内容维护动作最终都要服务于产品的工具能力，而不是反过来把产品拖成重内容项目。

后续若需要继续推进，可补：

- 配色资产字段草表  
- JSON 示例结构  
- 上新审核清单模板  
- CMS 低成本调研清单

---

## 10. 附录 A：配色资产字段草表

以下字段草表面向“发布态数据结构”，优先保证可落地与可维护，而不是一次性穷尽所有未来需求。

### 10.1 Base Color 字段草表

| 字段 | 必填 | 类型 | 示例 | 枚举/约束 | 用途说明 |
|------|------|------|------|-----------|----------|
| `id` | 是 | string | `bc_0001` | 全局唯一 | 基础单色主键 |
| `hex` | 是 | string | `#CBB7A1` | 标准 HEX | 渲染与机器标识 |
| `nameZh` | 是 | string | `雾杏` | 建议 1-12 字 | 中文名称 |
| `nameEn` | 否 | string | `Mist Apricot` | 可空 | 英文名称 |
| `tone` | 是 | enum | `warm` | `warm/cool/neutral` | 色温分类 |
| `lightnessLevel` | 是 | enum | `light` | `light/mid/deep` | 明度分层 |
| `saturationLevel` | 是 | enum | `low` | `low/mid/high` | 饱和度分层 |
| `colorFamily` | 是 | enum | `beige` | 受控集合 | 色系归类 |
| `styleTags` | 否 | string[] | `['urban']` | 建议 <= 5 | 风格标签 |
| `occasionTags` | 否 | string[] | `['citywalk']` | 建议 <= 5 | 场景标签 |
| `seasonTags` | 否 | string[] | `['spring']` | 建议 <= 4 | 季节标签 |
| `isNeutralCore` | 是 | boolean | `true` | - | 是否高复用中性色 |
| `status` | 是 | enum | `approved` | `draft/approved/archived` | 生命周期状态 |
| `notes` | 否 | string | `适合做辅色` | 可空 | 主理人备注 |

说明：

- `Base Color` 是资产原子层，重点用于复用与重组。  
- `hex` 必须稳定；命名和标签允许后续微调。  
- 发布态可以不带全部编辑备注，但建议保留 `status` 以便校验。

### 10.2 Palette 字段草表

| 字段 | 必填 | 类型 | 示例 | 枚举/约束 | 用途说明 |
|------|------|------|------|-----------|----------|
| `id` | 是 | string | `pl_0101` | 全局唯一 | 配色盘主键 |
| `slug` | 是 | string | `mist-city-morning` | 稳定别名 | 程序引用 |
| `nameZh` | 是 | string | `薄雾街角` | 建议 1-16 字 | 中文标题 |
| `nameEn` | 否 | string | `Mist City Morning` | 可空 | 英文标题 |
| `primaryColorId` | 是 | string | `bc_0001` | 必须存在 | 主色 |
| `secondaryColorId` | 是 | string | `bc_0008` | 必须存在 | 辅色 |
| `accentColorId` | 是 | string | `bc_0021` | 必须存在 | 点缀色 |
| `occasionId` | 是 | string | `citywalk` | 受控集合 | 主场景 |
| `moodTags` | 否 | string[] | `['clear','relaxed']` | 建议 <= 5 | 情绪标签 |
| `styleTags` | 否 | string[] | `['urban']` | 建议 <= 5 | 风格标签 |
| `seasonTags` | 否 | string[] | `['spring']` | 建议 <= 4 | 季节标签 |
| `genderLean` | 否 | enum | `female-leaning` | `neutral/female-leaning/male-leaning` | 偏向标签 |
| `safetyLevel` | 是 | enum | `safe` | `safe/expressive/experimental` | 安全等级 |
| `fitPhotoScenario` | 是 | boolean | `true` | - | 是否适合出片专题 |
| `heroTitleZh` | 否 | string | `今天的故事` | 可空 | Hero 标题覆盖 |
| `heroSubtitleZh` | 否 | string | `城市里的一点雾感` | 可空 | Hero 副标题 |
| `sourceType` | 是 | enum | `curated` | `curated/recomposed/generated` | 来源类型 |
| `sourceCollectionIds` | 否 | string[] | `['col_0001']` | 可空 | 所属专题 |
| `isPro` | 是 | boolean | `false` | - | 付费边界 |
| `status` | 是 | enum | `published` | `draft/approved/published/archived` | 生命周期状态 |
| `reviewer` | 否 | string | `owner` | 可空 | 审核人 |
| `reviewNotes` | 否 | string | `适合春季 citywalk` | 可空 | 审核备注 |
| `createdAt` | 否 | string | `2026-04-07T00:00:00Z` | ISO 时间 | 创建时间 |
| `updatedAt` | 否 | string | `2026-04-07T00:00:00Z` | ISO 时间 | 更新时间 |

说明：

- `Palette` 是用户直接感知的内容层。  
- `primary / secondary / accent` 三角色必须始终清晰，不建议在数据层做模糊表达。  
- `sourceType=generated` 不表示直接对用户开放生成，而是表示该资产来自规则或算法补全后又经人工审核入库。

### 10.3 Collection 字段草表

| 字段 | 必填 | 类型 | 示例 | 枚举/约束 | 用途说明 |
|------|------|------|------|-----------|----------|
| `id` | 是 | string | `col_0001` | 全局唯一 | 专题主键 |
| `nameZh` | 是 | string | `Citywalk 拍照` | 建议 1-16 字 | 中文名称 |
| `nameEn` | 否 | string | `Citywalk Photo` | 可空 | 英文名称 |
| `themeType` | 是 | enum | `scene` | `scene/season/festival/style/commercial` | 专题类型 |
| `descriptionZh` | 否 | string | `适合城市漫游与轻拍照。` | 可空 | 专题说明 |
| `paletteIds` | 是 | string[] | `['pl_0101']` | 至少 1 个 | 收录内容 |
| `coverPaletteId` | 是 | string | `pl_0101` | 必须存在 | 专题封面 |
| `occasionTags` | 否 | string[] | `['citywalk']` | 可空 | 场景标签 |
| `styleTags` | 否 | string[] | `['urban']` | 可空 | 风格标签 |
| `isPro` | 是 | boolean | `false` | - | 是否 Pro 专题 |
| `releaseMode` | 是 | enum | `permanent` | `permanent/seasonal/limited` | 发布模式 |
| `status` | 是 | enum | `published` | `planning/ready/published/archived` | 生命周期状态 |

说明：

- `Collection` 是运营与商业化编组层，不替代 `Palette`。  
- 一个 `Palette` 可以进入多个 `Collection`，但应避免在同一时期被过多主题重复消费。  
- `releaseMode` 将直接影响后续数据更新与下架策略。

---

## 11. 附录 B：JSON 示例结构

以下 JSON 仅用于说明最小可行发布态结构，不等于最终文件名或运行时装载方式已拍板。

### 11.1 `base-colors.json` 示例

```json
{
	"version": 1,
	"updatedAt": "2026-04-07T00:00:00Z",
	"items": [
		{
			"id": "bc_0001",
			"hex": "#CBB7A1",
			"nameZh": "雾杏",
			"nameEn": "Mist Apricot",
			"tone": "warm",
			"lightnessLevel": "light",
			"saturationLevel": "low",
			"colorFamily": "beige",
			"styleTags": ["urban", "soft"],
			"occasionTags": ["citywalk", "commute"],
			"seasonTags": ["spring", "autumn"],
			"isNeutralCore": true,
			"status": "approved"
		},
		{
			"id": "bc_0008",
			"hex": "#7D8791",
			"nameZh": "石雾灰蓝",
			"nameEn": "Stone Mist Blue",
			"tone": "cool",
			"lightnessLevel": "mid",
			"saturationLevel": "low",
			"colorFamily": "blue",
			"styleTags": ["urban"],
			"occasionTags": ["citywalk"],
			"seasonTags": ["spring", "summer"],
			"isNeutralCore": false,
			"status": "approved"
		}
	]
}
```

### 11.2 `palettes.json` 示例

```json
{
	"version": 1,
	"updatedAt": "2026-04-07T00:00:00Z",
	"items": [
		{
			"id": "pl_0101",
			"slug": "mist-city-morning",
			"nameZh": "薄雾街角",
			"nameEn": "Mist City Morning",
			"primaryColorId": "bc_0001",
			"secondaryColorId": "bc_0008",
			"accentColorId": "bc_0021",
			"occasionId": "citywalk",
			"moodTags": ["clear", "relaxed"],
			"styleTags": ["urban"],
			"seasonTags": ["spring"],
			"safetyLevel": "safe",
			"fitPhotoScenario": true,
			"heroTitleZh": "今天的故事",
			"heroSubtitleZh": "城市里的一点雾感",
			"sourceType": "curated",
			"sourceCollectionIds": ["col_0001"],
			"isPro": false,
			"status": "published",
			"reviewer": "owner",
			"reviewNotes": "适合城市漫游与轻拍照",
			"createdAt": "2026-04-07T00:00:00Z",
			"updatedAt": "2026-04-07T00:00:00Z"
		}
	]
}
```

### 11.3 `collections.json` 示例

```json
{
	"version": 1,
	"updatedAt": "2026-04-07T00:00:00Z",
	"items": [
		{
			"id": "col_0001",
			"nameZh": "Citywalk 拍照",
			"nameEn": "Citywalk Photo",
			"themeType": "scene",
			"descriptionZh": "适合城市漫游和轻拍照的清爽都市系配色。",
			"paletteIds": ["pl_0101", "pl_0102", "pl_0103"],
			"coverPaletteId": "pl_0101",
			"occasionTags": ["citywalk"],
			"styleTags": ["urban"],
			"isPro": false,
			"releaseMode": "permanent",
			"status": "published"
		}
	]
}
```

### 11.4 文件级补充约束

建议约束：

1. 每个 JSON 文件保留独立 `version` 与 `updatedAt`。  
2. 发布态尽量避免保留过多纯编辑备注字段。  
3. `Palette` 中的颜色引用必须能在 `Base Color` 中找到。  
4. `Collection.coverPaletteId` 必须存在于 `paletteIds` 中。  
5. 后续若支持远端覆盖，建议以整个文件或分专题文件为单位下发，而不是逐条散发。