# 市场参考驱动的配色资产运营方案

> 状态：草案 v0.1（2026-05-07）
> 上游产品文档：[`../../product/color-asset-admin-prd.md`](../../product/color-asset-admin-prd.md)
> 上游技术文档：[`../../architecture/color-asset-admin-technical-architecture.md`](../../architecture/color-asset-admin-technical-architecture.md)
> 数据合同：[`../../architecture/color-asset-admin-data-contract.md`](../../architecture/color-asset-admin-data-contract.md)
> 首轮矩阵：[`./palette-phase-1-target-matrix.md`](./palette-phase-1-target-matrix.md)
> 采样台账模板：[`./palette-market-sampling-ledger-template.md`](./palette-market-sampling-ledger-template.md)
> 首批采样骨架：[`./workday-market-sampling-batch-01.md`](./workday-market-sampling-batch-01.md)

## 1. 文档定位

本文用于定义 DayPalette 下一阶段“配色资产运营”的共享层执行方案，重点收口以下判断：

1. 首轮资产扩充不再依赖主观拍脑袋产出，而采用“公开服饰市场参考 + 人工提炼”的方法。
2. 首轮目标不是在现有 17 个 palette 上修修补补，而是完整重建 160 个新 palette，并把现有 17 个整体转入归档层。
3. 月更阶段不追求持续堆总量，而采用固定库容下的少量精选与等量归档。
4. 后台需要补齐溯源、审核和批次字段，才能支撑长期运营，而不是靠备注栏临时记录。

---

## 2. 一句话结论

DayPalette 的配色资产运营应切换到一个**市场参考驱动、人工策展定稿、固定库容滚动更新**的模型：首轮按 4 个场景各重建 40 个 palette，共 160 个；现有 17 个 palette 在新库上线后全部转为 `archived`；后续每月只精选补充 4 到 6 个新 palette，并同步归档等量旧 palette，保持资产库克制、稳定、可解释。

---

## 3. 当前已确认的运营判断

### 3.1 资产规模

首轮正式目标：

1. `workday`：40 个
2. `weekend`：40 个
3. `outdoor`：40 个
4. `dinner`：40 个

合计 160 个正式 palette。

### 3.2 旧资产处置

当前 `day_palette` 中已有的 17 个 palette 不作为新库继续沿用。

处理原则：

1. 不执行 `deleted`，而执行 `archived`。
2. 旧资产保留历史 ID 和引用关系，用于回看、比对和必要时恢复。
3. 归档动作应在新库通过审核并准备切换后统一执行，避免中途出现空档。

### 3.3 审美与风险边界

运营判断继续服从共享层既有原则：

1. 优先“可穿、稳妥、可解释”，不追求戏剧化配色。
2. 允许少量 expressive 候选，但不把 experimental 作为首轮常规配置。
3. 资产生产必须服务真实穿搭场景，而不是抽象色板练习。

---

## 4. 分阶段执行方案

## 4.1 阶段 0：冻结当前库并先补规则

在正式生产 160 个 palette 之前，先完成以下准备动作：

1. 冻结当前 17 个 palette，不再继续增量修补。
2. 固定首轮 4 个场景与其 5 个子主题，详见 [`./palette-phase-1-target-matrix.md`](./palette-phase-1-target-matrix.md)。
3. 固定每个子主题 8 个正式 palette 的产出目标。
4. 固定每个场景 `30 safe + 10 expressive` 的结构比例。
5. 在后台数据合同中补齐溯源字段、审核字段和归档说明字段，详见 [`../../architecture/color-asset-admin-data-contract.md`](../../architecture/color-asset-admin-data-contract.md)。

阶段 0 的目的不是“做一点内容”，而是防止首轮 160 个资产一开始就失去口径。

## 4.2 阶段 1：重建首轮 160 个 palette

阶段 1 建议采用“480 个市场参考样本 -> 240 个候选组合 -> 160 个正式 palette”的三层漏斗。

### 4.2.1 市场参考采样

采样目标：

1. 每个场景 120 套公开服饰参考。
2. 每个子主题 24 套公开服饰参考。
3. 首轮总样本量 480 套。

采样来源优先级：

1. 品牌官网公开 lookbook / editorial 页面。
2. 品牌官方旗舰店公开商品页。
3. 大型多品牌服饰电商公开商品页。
4. 大型电商平台中品牌官方店的公开商品页。

采样时只提取以下信息：

1. 服饰场景与子主题归属。
2. 主色、辅色、点缀色及其角色关系。
3. 品类结构，例如外套 / 上衣 / 下装 / 鞋包。
4. 季节性、材质感、整体风格与亮点。
5. 平台、品牌、链接、采样日期和内部备注。

建议直接使用 [`./palette-market-sampling-ledger-template.md`](./palette-market-sampling-ledger-template.md) 建立首轮台账，避免字段口径漂移。

### 4.2.2 候选 palette 生成

从市场参考进入候选库时，必须完成一次“去原样化”提炼：

1. 每个候选 palette 至少组合 3 个参考样本的颜色关系，不做 1 比 1 翻译。
2. 先抽象出颜色角色关系，再回到 DayPalette 的 `primary / secondary / accent` 结构。
3. 名称、hero 文案、标签和说明全部重写，不复用原商品名或品牌表达。
4. 候选库阶段每个子主题先保留 12 个候选，共 240 个候选。

### 4.2.3 正式 palette 定稿

正式库筛选标准：

1. 每个子主题保留 8 个正式 palette。
2. 每个子主题建议 `6 safe + 2 expressive`。
3. 同一子主题内，不允许出现过多近似主色与近似点缀色的重复组合。
4. 所有 `approved` 或 `published` palette 都必须经过人工审核，不允许只凭批处理直接入库。

### 4.2.4 旧 17 个 palette 归档

在 160 个新 palette 通过审核后，现有 17 个 palette 统一执行：

1. `status` 改为 `archived`。
2. 写入归档时间与归档原因。
3. 保留在后台可检索视图中，但默认不出现在活跃列表。

建议统一归档原因：

`phase-1-full-rebuild-replaced`

## 4.3 阶段 2：进入日常月更

阶段 2 的核心不是继续无限扩容，而是固定库容、低频精选。

建议节奏：

1. 每月新增 4 到 6 个新 palette。
2. 每月同步归档 4 到 6 个重复度最高、表现最弱或季节性过期的旧 palette。
3. 每季度可以围绕 1 个场景做一次 12 到 16 个的专题增强，但不强制扩大总活跃库容。

月更仍然沿用阶段 1 的方法，只缩小采样规模：

1. 月更前先做 24 到 36 套公开服饰参考采样。
2. 先形成 8 到 12 个候选。
3. 最终只发布 4 到 6 个正式 palette。

---

## 5. 法务与创意边界

## 5.1 市场参考的正确用途

公开服饰平台在本方案中只承担“研究样本”角色，而不是可直接复用的内容资产。

允许：

1. 观察真实服饰市场中高频出现的颜色关系。
2. 抽象出可穿搭、可解释的颜色结构。
3. 把多个来源的共同趋势转化为新的 DayPalette palette。

不允许：

1. 直接复制某一套商品图或 lookbook 的完整配色表达。
2. 在 App、后台演示或共享仓中保存并发布原图素材。
3. 复用品牌名、商品名、广告语或系列名作为最终对外表达。

## 5.2 首轮合规约束

首轮建议强制遵守以下规则：

1. `referenceMethod=market-sampled` 的正式 palette 至少对应 3 条参考记录。
2. 这 3 条参考记录至少来自 2 个不同平台。
3. 这 3 条参考记录至少来自 2 个不同品牌。
4. 参考记录入库时保存链接、平台、品牌、采样日期和内部说明，不在 Git 仓中提交截图。
5. 若确实需要临时截图做内部评审，应保存在 Git 外部的本地工作目录，而不是进入真相源仓库。

---

## 6. 首轮审核口径

每个候选 palette 至少通过以下检查：

1. 是否明显符合目标场景与子主题。
2. `primary / secondary / accent` 的角色是否清晰。
3. 是否更像真实穿搭会出现的组合，而不是色卡拼贴。
4. 是否与现有候选或已发布 palette 过度重复。
5. 是否存在明显高风险脏色、冲突色或不耐看的点缀。
6. 命名、双语文案和标签是否为 DayPalette 自有表达。

审核结论建议统一使用：

1. `pending`
2. `needsRevision`
3. `approved`
4. `rejected`

---

## 7. 后台需要支持的运营元数据

为支撑本方案，后台不应只维护最终色盘本身，还应支持以下运营元数据：

1. `referenceMethod`：记录该 palette 是市场采样、编辑推导还是内部重组。
2. `referenceSources[]`：记录平台、品牌、链接、采样日期、品类与颜色摘要。
3. `marketSignalSummary`：记录该 palette 提炼自哪些市场趋势。
4. `productionBatchId`：记录属于哪一轮重建或哪一月更新批次。
5. `reviewStatus`、`reviewedAt`、`reviewer`、`reviewNotes`：记录审核过程。
6. `archivedAt`、`archiveReason`：支持首轮替换和后续月更的等量归档。

详细字段合同见 [`../../architecture/color-asset-admin-data-contract.md`](../../architecture/color-asset-admin-data-contract.md)。

---

## 8. 当前阶段建议的最小实施顺序

如果要把本文转成执行动作，建议顺序如下：

1. 先在后台数据合同中补字段，而不是先让运营用备注字段硬记。
2. 再按 [`./palette-phase-1-target-matrix.md`](./palette-phase-1-target-matrix.md) 建立首轮采样清单。
3. 先做每个子主题 24 条参考记录，再做候选聚类。
4. 先完成 240 个候选，再筛入 160 个正式 palette。
5. 最后统一归档旧 17 个，并开始月更节奏。

---

## 9. 当前阶段结论

DayPalette 下一阶段最重要的，不是“快速把 palette 数量补到 160”，而是先把**生产方法**切到公开市场参考驱动，再把**审核和溯源记录**落到后台字段里。只有这样，160 个 palette 才不是一次性堆量，而是日后可以持续复用的方法论起点。