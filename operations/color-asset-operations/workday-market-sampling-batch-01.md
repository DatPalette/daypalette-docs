# Workday 场景首批 24 条市场采样台账骨架

> 状态：草案 v0.1（2026-05-07）
> 上游模板：[`./palette-market-sampling-ledger-template.md`](./palette-market-sampling-ledger-template.md)
> 上游矩阵：[`./palette-phase-1-target-matrix.md`](./palette-phase-1-target-matrix.md)

## 1. 文档定位

本文是 `workday` 场景的首批采样骨架，用于先跑通市场采样、候选聚类和后台录入链路。

这 24 条不是 `workday` 全量目标，只是第一批试运行样本。

当前建议：

1. 先用这 24 条样本验证采样节奏、字段口径和溯源记录方式。
2. 验证通过后，再继续补齐 `workday` 全量 120 条样本。
3. 这 24 条覆盖 5 个子主题，但不是等量分配。

---

## 2. 批次说明

固定字段建议如下：

| 字段 | 值 |
| --- | --- |
| `productionBatchId` | `phase1-workday-batch01` |
| `occasionId` | `workday` |
| `digestionStatus` 初始值 | `sampled` |

子主题分配建议：

1. `polished-light-commute` / 轻正式通勤：5 条
2. `urban-minimal-foundation` / 都市极简基底：5 条
3. `soft-tone-lift` / 柔和提气色：5 条
4. `mist-cool-commute` / 雨雾冷静通勤：5 条
5. `warm-grounded-commute` / 暖稳秋冬通勤：4 条

建议先填顺序：

1. 先填 `brand-site` 与 `brand-flagship-store`。
2. 每个子主题先至少填 2 条不同品牌。
3. 先补 `platform / brandName / sourceUrl / observedAt / colorSummary`，再回写 `marketSignals` 与 `notes`。

---

## 3. 首批 24 条骨架

| samplingId | productionBatchId | occasionId | themeKey | themeLabelZh | platform | channelType | brandName | sourceUrl | observedAt | itemCategory | seasonHint | primaryColorSummary | secondaryColorSummary | accentColorSummary | colorSummary | styleSignals | marketSignals | notes | candidatePaletteIds | finalPaletteIds | digestionStatus |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| sam_workday_b01_001 | phase1-workday-batch01 | workday | polished-light-commute | 轻正式通勤 |  | brand-site |  |  |  | blazer | spring |  |  |  |  | commute, polished |  | 优先看轻正式外套与内搭层次 |  |  | sampled |
| sam_workday_b01_002 | phase1-workday-batch01 | workday | polished-light-commute | 轻正式通勤 |  | brand-flagship-store |  |  |  | shirt | spring |  |  |  |  | clean, formal |  | 优先看白衬衫与灰蓝、卡其的关系 |  |  | sampled |
| sam_workday_b01_003 | phase1-workday-batch01 | workday | polished-light-commute | 轻正式通勤 |  | multi-brand-platform |  |  |  | trousers | spring |  |  |  |  | polished, minimal |  | 重点采样低风险下装色 |  |  | sampled |
| sam_workday_b01_004 | phase1-workday-batch01 | workday | polished-light-commute | 轻正式通勤 |  | brand-site |  |  |  | cardigan | autumn |  |  |  |  | soft, polished |  | 观察针织通勤配色的温和提气色做法 |  |  | sampled |
| sam_workday_b01_005 | phase1-workday-batch01 | workday | polished-light-commute | 轻正式通勤 |  | marketplace-brand-store |  |  |  | trench | autumn |  |  |  |  | refined, urban |  | 优先看风衣与内搭的低对比组合 |  |  | sampled |
| sam_workday_b01_006 | phase1-workday-batch01 | workday | urban-minimal-foundation | 都市极简基底 |  | brand-site |  |  |  | coat | winter |  |  |  |  | urban, minimal |  | 控制黑白灰蓝的层次，不做纯无彩堆叠 |  |  | sampled |
| sam_workday_b01_007 | phase1-workday-batch01 | workday | urban-minimal-foundation | 都市极简基底 |  | multi-brand-platform |  |  |  | knitwear | winter |  |  |  |  | minimal, commute |  | 观察深浅灰与墨蓝之间的层次关系 |  |  | sampled |
| sam_workday_b01_008 | phase1-workday-batch01 | workday | urban-minimal-foundation | 都市极简基底 |  | brand-flagship-store |  |  |  | shirt | all |  |  |  |  | urban, clean |  | 看简洁衬衫与西裤的三色重心 |  |  | sampled |
| sam_workday_b01_009 | phase1-workday-batch01 | workday | urban-minimal-foundation | 都市极简基底 |  | brand-site |  |  |  | blazer | autumn |  |  |  |  | sharp, minimal |  | 保留少量冷感都市路线 |  |  | sampled |
| sam_workday_b01_010 | phase1-workday-batch01 | workday | urban-minimal-foundation | 都市极简基底 |  | marketplace-brand-store |  |  |  | dress | spring |  |  |  |  | urban, restrained |  | 看极简连衣裙通勤路线是否可转译 |  |  | sampled |
| sam_workday_b01_011 | phase1-workday-batch01 | workday | soft-tone-lift | 柔和提气色 |  | brand-site |  |  |  | blouse | spring |  |  |  |  | soft, fresh |  | 重点观察杏、壳白、雾粉、雾紫 |  |  | sampled |
| sam_workday_b01_012 | phase1-workday-batch01 | workday | soft-tone-lift | 柔和提气色 |  | brand-flagship-store |  |  |  | cardigan | spring |  |  |  |  | gentle, polished |  | 看柔和提气色但不过分甜感的路线 |  |  | sampled |
| sam_workday_b01_013 | phase1-workday-batch01 | workday | soft-tone-lift | 柔和提气色 |  | multi-brand-platform |  |  |  | skirt | summer |  |  |  |  | airy, soft |  | 观察下装是否用更浅中性色托住气色点缀 |  |  | sampled |
| sam_workday_b01_014 | phase1-workday-batch01 | workday | soft-tone-lift | 柔和提气色 |  | brand-site |  |  |  | knitwear | autumn |  |  |  |  | soft, urban |  | 允许低饱和莓色或烟紫做小点缀 |  |  | sampled |
| sam_workday_b01_015 | phase1-workday-batch01 | workday | soft-tone-lift | 柔和提气色 |  | marketplace-brand-store |  |  |  | blazer | autumn |  |  |  |  | polished, feminine-leaning |  | 重点避免过甜和过少女感 |  |  | sampled |
| sam_workday_b01_016 | phase1-workday-batch01 | workday | mist-cool-commute | 雨雾冷静通勤 |  | brand-site |  |  |  | trench | spring |  |  |  |  | calm, urban |  | 偏蓝灰、雾绿、石色，不做亮 accent |  |  | sampled |
| sam_workday_b01_017 | phase1-workday-batch01 | workday | mist-cool-commute | 雨雾冷静通勤 |  | brand-flagship-store |  |  |  | blazer | autumn |  |  |  |  | cool, restrained |  | 看阴雨天气常见的灰蓝与冷米色 |  |  | sampled |
| sam_workday_b01_018 | phase1-workday-batch01 | workday | mist-cool-commute | 雨雾冷静通勤 |  | multi-brand-platform |  |  |  | shirt | all |  |  |  |  | clean, quiet |  | 重点采样雾感冷色而非高对比商务色 |  |  | sampled |
| sam_workday_b01_019 | phase1-workday-batch01 | workday | mist-cool-commute | 雨雾冷静通勤 |  | brand-site |  |  |  | knitwear | winter |  |  |  |  | quiet, calm |  | 看灰蓝针织与深色外套的稳感关系 |  |  | sampled |
| sam_workday_b01_020 | phase1-workday-batch01 | workday | mist-cool-commute | 雨雾冷静通勤 |  | marketplace-brand-store |  |  |  | trousers | winter |  |  |  |  | muted, urban |  | 观察下装是否提供更稳的压舱色 |  |  | sampled |
| sam_workday_b01_021 | phase1-workday-batch01 | workday | warm-grounded-commute | 暖稳秋冬通勤 |  | brand-site |  |  |  | coat | winter |  |  |  |  | grounded, warm |  | 重点看咖棕、炭灰、酒红棕的克制搭法 |  |  | sampled |
| sam_workday_b01_022 | phase1-workday-batch01 | workday | warm-grounded-commute | 暖稳秋冬通勤 |  | brand-flagship-store |  |  |  | knitwear | winter |  |  |  |  | grounded, refined |  | 避免老气和过厚重 |  |  | sampled |
| sam_workday_b01_023 | phase1-workday-batch01 | workday | warm-grounded-commute | 暖稳秋冬通勤 |  | multi-brand-platform |  |  |  | blazer | autumn |  |  |  |  | warm, urban |  | 看棕调通勤的三色主次是否清晰 |  |  | sampled |
| sam_workday_b01_024 | phase1-workday-batch01 | workday | warm-grounded-commute | 暖稳秋冬通勤 |  | marketplace-brand-store |  |  |  | skirt | autumn |  |  |  |  | grounded, polished |  | 允许一点暖酒红，但不能抢主色 |  |  | sampled |

---

## 4. 使用建议

1. 先从 `brand-site` 与 `brand-flagship-store` 两类来源开始，优先保证样本质量。
2. 每填完 8 条，先回看是否已经出现重复品牌、重复色路或重复品类，再继续采。
3. 当这 24 条采样填完后，再开始第一次候选聚类，不要边采边直接定 palette。
4. 如果某个子主题明显采不出稳定色路，应先调整子主题定义，而不是强行凑数。