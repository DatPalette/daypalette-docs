# DayPalette 内容系统概览

## 1. 目标

DayPalette 的内容系统不是简单的颜色列表，而是一套为“今日配色推荐”服务的多层资产体系。

它的目标是：

- 让移动端可以稳定消费结构化内容
- 让工作台可以进行人工策展与质量控制
- 让后续扩容、版本管理、远端覆盖具备基础边界

## 2. 核心对象

### 2.1 Base Color

最小颜色原子层。

典型字段：

- `id`
- `hex`
- `tone`
- `lightnessLevel`
- `saturationLevel`
- `colorFamily`
- `styleTags`
- `occasionTags`
- `seasonTags`
- `isNeutralCore`
- `status`

### 2.2 Palette

面向用户展示的三色配色盘。

典型字段：

- `primaryColorId`
- `secondaryColorId`
- `accentColorId`
- `occasionId`
- `moodTags`
- `styleTags`
- `seasonTags`
- `safetyLevel`
- `fitPhotoScenario`
- `status`

### 2.3 Collection

按场景或主题组织多个 palette 的合集层。

它的价值是：

- 给运营提供更稳定的主题组织方式
- 给产品提供可发布的内容打包边界

### 2.4 Curation State

运营层状态，不直接面向最终用户。

它回答的是：

- 哪些候选进入短名单
- 哪些候选可以打包
- 哪些候选被淘汰以及为什么

## 3. 内容链路

当前内容链路应理解为三层：

1. `source/current/`：编辑态资产
2. `curation-state.v1.json`：运营层判断
3. `bundle/current/`：移动端消费态输出

这三层不要混在一起。

## 4. 当前产品策略

DayPalette 当前更适合“保守、稳定、可解释”的配色策略，而不是高风险的大跨度搭配。

因此在内容策展上，优先强调：

- 场景适配
- 安全等级
- 可复用的中性色基础
- 可人工审核的少量 expressive 候选

## 5. 当前阶段结论

当前阶段最重要的不是把系统做成 CMS，而是先稳定这三件事：

1. source 结构化
2. 策展状态可沉淀
3. bundle 生成边界明确

只要这三件事稳定，后续无论扩内容、接远端、做多人协作，都会轻很多。
