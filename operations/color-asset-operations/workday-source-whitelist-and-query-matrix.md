# Workday 场景来源白名单与搜索词矩阵

> 状态：草案 v0.1（2026-05-07）
> 上游运营方案：[`./palette-market-reference-production-plan.md`](./palette-market-reference-production-plan.md)
> 上游矩阵：[`./palette-phase-1-target-matrix.md`](./palette-phase-1-target-matrix.md)
> 首批采样骨架：[`./workday-market-sampling-batch-01.md`](./workday-market-sampling-batch-01.md)

## 1. 文档定位

本文用于把 `workday` 场景的采样入口先收口成一套可执行的白名单与搜索词矩阵，解决以下问题：

1. 运营开始采样时，不知道优先去哪些公开页面找样本。
2. 每次采样都临时想关键词，导致结果波动大。
3. 采样主题与首轮矩阵脱节，后续候选聚类难以归类。

---

## 2. 来源白名单

## 2.1 当前允许的渠道类型

`workday` 首批采样只建议使用以下四类公开来源：

1. `brand-site`
   适合：品牌官网 lookbook、editorial、新品专题页。

2. `brand-flagship-store`
   适合：品牌官方旗舰店公开商品页。

3. `multi-brand-platform`
   适合：多品牌服饰零售平台的公开商品页与专题页。

4. `marketplace-brand-store`
   适合：大型电商平台中的品牌官方店公开页面。

## 2.2 当前不建议优先使用的来源

`workday` 首批采样不建议优先依赖：

1. 非官方搬运图集。
2. 社交平台二次转载截图。
3. 无品牌归属的拼接穿搭图。
4. 无法回溯到公开页面的单张图片。

理由：这些来源很难稳定记录 `platform / brandName / sourceUrl`，后续不利于审核与复查。

## 2.3 优先采样品类

首批 `workday` 采样优先看以下品类：

1. `blazer`
2. `shirt`
3. `cardigan`
4. `trench`
5. `coat`
6. `knitwear`
7. `trousers`
8. `skirt`
9. `dress`

优先顺序建议：

1. 先采外套 + 内搭关系明显的页面。
2. 再采能看清上下装关系的商品页。
3. 最后再采单品色感强但搭配关系弱的页面。

---

## 3. `workday` 子主题 key 对照

| key | 中文名 | 主轴 |
| --- | --- | --- |
| `polished-light-commute` | 轻正式通勤 | 轻正式、低风险、清爽有秩序 |
| `urban-minimal-foundation` | 都市极简基底 | 黑白灰蓝的层次与克制对比 |
| `soft-tone-lift` | 柔和提气色 | 低饱和暖柔色带来的气色提升 |
| `mist-cool-commute` | 雨雾冷静通勤 | 蓝灰、雾绿、石色的冷静语义 |
| `warm-grounded-commute` | 暖稳秋冬通勤 | 棕、灰、酒红棕的稳感与厚度 |

---

## 4. 搜索词结构

## 4.1 中文结构

建议结构：

`场景词 + 子主题词 + 品类词 + 季节词 + 品牌/渠道限定`

示例：

1. `通勤 轻正式 西装 春夏 品牌官网`
2. `通勤 极简 衬衫 秋冬 官方旗舰店`
3. `通勤 柔和气色 针织 春季 女装 官方店`

## 4.2 英文结构

建议结构：

`occasion term + theme term + category term + season term + channel qualifier`

示例：

1. `workwear polished blazer spring brand site`
2. `office minimal shirt autumn flagship store`
3. `soft commute cardigan spring official store`

---

## 5. 子主题搜索词矩阵

## 5.1 `polished-light-commute`

中文建议词：

1. `通勤 轻正式 西装`
2. `通勤 轻正式 衬衫`
3. `春夏 通勤 风衣`
4. `工作日 轻通勤 针织`

英文建议词：

1. `polished workwear blazer`
2. `light office commute shirt`
3. `spring workwear trench`
4. `soft office cardigan`

## 5.2 `urban-minimal-foundation`

中文建议词：

1. `通勤 极简 西装`
2. `都市 极简 衬衫`
3. `黑白灰 通勤 大衣`
4. `极简 通勤 连衣裙`

英文建议词：

1. `urban minimal blazer`
2. `minimal office shirt`
3. `grey navy work coat`
4. `minimal work dress`

## 5.3 `soft-tone-lift`

中文建议词：

1. `通勤 柔和气色 衬衫`
2. `杏色 通勤 针织`
3. `雾粉 通勤 开衫`
4. `低饱和 通勤 女装 春季`

英文建议词：

1. `soft tone office blouse`
2. `apricot workwear knit`
3. `muted pink office cardigan`
4. `soft color commute spring`

## 5.4 `mist-cool-commute`

中文建议词：

1. `通勤 蓝灰 风衣`
2. `冷感 通勤 西装`
3. `雾感 灰蓝 衬衫`
4. `雨天 通勤 针织`

英文建议词：

1. `mist blue work trench`
2. `cool tone office blazer`
3. `grey blue work shirt`
4. `rainy commute knitwear`

## 5.5 `warm-grounded-commute`

中文建议词：

1. `秋冬 通勤 棕色 大衣`
2. `暖调 通勤 针织`
3. `酒红棕 通勤 西装`
4. `秋冬 通勤 半裙`

英文建议词：

1. `warm office coat`
2. `brown commute knitwear`
3. `burgundy brown blazer`
4. `autumn office skirt`

---

## 6. 采样执行建议

执行顺序建议：

1. 先按子主题 `key` 建立采样批次文件夹或表格筛选视图。
2. 每个子主题先用 2 个中文词组 + 2 个英文词组试跑。
3. 每个子主题先收 8 到 10 个候选链接，再筛进正式台账。
4. 若某组关键词结果过于偏时尚 editorial、缺少真实穿搭关系，优先替换品类词，而不是先扩更多平台。

---

## 7. 当前阶段结论

`workday` 首批采样现在至少已经有一套稳定的入口：知道优先去哪找、知道各子主题该搜什么、也知道先看哪些品类。这还不是采样助手本身，但已经把最容易反复卡住的“搜什么”和“去哪里找”先固定住了。