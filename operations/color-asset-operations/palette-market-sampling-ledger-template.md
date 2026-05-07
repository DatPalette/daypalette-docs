# 配色市场采样台账模板

> 状态：草案 v0.1（2026-05-07）
> 上游运营方案：[`./palette-market-reference-production-plan.md`](./palette-market-reference-production-plan.md)
> 上游矩阵：[`./palette-phase-1-target-matrix.md`](./palette-phase-1-target-matrix.md)

## 1. 模板目的

本文用于给首轮 160 个 palette 重建和后续月更提供一份统一采样台账模板。

目标不是把所有判断都塞进后台第一版，而是先确保采样记录至少满足：

1. 能回看来源平台、品牌和链接。
2. 能快速知道它属于哪个场景和子主题。
3. 能清楚记录主色 / 辅色 / 点缀色关系。
4. 能追踪它最后是否被吸收到候选 palette 或正式 palette。

---

## 2. 建议记录字段

| 字段 | 说明 | 示例 |
| --- | --- | --- |
| `samplingId` | 采样记录 ID | `sam_workday_001` |
| `productionBatchId` | 对应生产批次 | `phase1-workday-v1` |
| `occasionId` | 场景 ID | `workday` |
| `themeKey` | 子主题 key | `soft-commute-polish` |
| `platform` | 平台名称 | `brand-site` |
| `channelType` | 渠道类型 | `brand-site` |
| `brandName` | 品牌名称 | `Example Brand A` |
| `sourceUrl` | 公开页面链接 | `https://example.com/look-1` |
| `observedAt` | 采样时间 | `2026-05-07T09:00:00Z` |
| `itemCategory` | 主要品类 | `shirt` |
| `seasonHint` | 季节提示 | `spring` |
| `primaryColorSummary` | 主色摘要 | `shell white` |
| `secondaryColorSummary` | 辅色摘要 | `mist blue` |
| `accentColorSummary` | 点缀色摘要 | `soft plum` |
| `colorSummary` | 颜色摘要数组 | `shell white, mist blue, soft plum` |
| `styleSignals` | 风格信号 | `commute, polished, soft` |
| `marketSignals` | 市场信号摘要 | `2026 春季通勤样本里低饱和雾蓝出现频次高` |
| `notes` | 内部分析备注 | `适合转译为柔和气色通勤子主题` |
| `candidatePaletteIds` | 已吸收到的候选 palette | `cand_workday_004, cand_workday_011` |
| `finalPaletteIds` | 已吸收到的正式 palette | `w101` |
| `digestionStatus` | 消化状态 | `sampled / clustered / shortlisted / published / rejected` |

---

## 3. Markdown 表格模板

| samplingId | productionBatchId | occasionId | themeKey | platform | channelType | brandName | sourceUrl | observedAt | itemCategory | seasonHint | primaryColorSummary | secondaryColorSummary | accentColorSummary | colorSummary | styleSignals | marketSignals | notes | candidatePaletteIds | finalPaletteIds | digestionStatus |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| sam_workday_001 | phase1-workday-v1 | workday | soft-commute-polish | brand-site | brand-site | Example Brand A | https://example.com/look-1 | 2026-05-07T09:00:00Z | shirt | spring | shell white | mist blue | soft plum | shell white, mist blue, soft plum | commute, polished, soft | 2026 春季通勤样本中雾蓝与壳白重复出现 | 适合柔和气色通勤子主题 | cand_workday_004 | w101 | shortlisted |
| sam_workday_002 | phase1-workday-v1 | workday | rain-muted-urban | multi-brand-platform | multi-brand-platform | Example Brand B | https://example.com/look-2 | 2026-05-07T09:20:00Z | blazer | autumn | cloud grey | slate blue | ink navy | cloud grey, slate blue, ink navy | urban, minimal, cool | 冷静蓝灰在雨雾通勤样本中出现频次高 | 更适合雨雾冷静子主题 | cand_workday_009 |  | clustered |

---

## 4. CSV 列顺序建议

如果后续要导入表格工具或脚本，建议列顺序如下：

```text
samplingId,productionBatchId,occasionId,themeKey,platform,channelType,brandName,sourceUrl,observedAt,itemCategory,seasonHint,primaryColorSummary,secondaryColorSummary,accentColorSummary,colorSummary,styleSignals,marketSignals,notes,candidatePaletteIds,finalPaletteIds,digestionStatus
```

---

## 5. 使用建议

1. 首轮 160 个重建时，先按矩阵把 `occasionId + themeKey + productionBatchId` 固定下来，再开始采样。
2. 一条采样记录只对应一条公开样本，不要把多个样本揉成一条。
3. `candidatePaletteIds` 和 `finalPaletteIds` 允许留空，等候选筛选后再补。
4. `digestionStatus` 应显式更新，否则后续很难判断哪些样本已经被消费。
5. 如果暂时不用后台录入，这份模板至少应先落在表格或文档中，保持字段口径一致。