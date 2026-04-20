# 工作台策展流程

## 1. 工作台的定位

DayPalette 工作台优先服务的是“看和选”，而不是传统后台式的“填表和增删改查”。

当前优先级顺序：

1. 预览
2. 筛选
3. 对比
4. 标记与状态沉淀
5. 基础编辑
6. 发布输出

## 2. 核心工作流

### 2.1 批量浏览

目标：

- 快速看完同一场景下的大量候选
- 先做第一轮淘汰，而不是直接进入细节编辑

### 2.2 过滤与搜索

常用过滤维度：

- 场景
- `reviewStatus`
- `safetyLevel`
- 风格标签
- 情绪标签
- 是否已选中

### 2.3 对比

目标：

- 把肉眼接近的候选放在一起比较
- 快速决定保留、短名单还是淘汰

### 2.4 状态沉淀

建议的运营层状态：

- `draft`：还未明确判断
- `shortlisted`：进入短名单，保留继续观察
- `approved`：可以进入 bundle 候选
- `rejected`：淘汰，不进入 bundle

常用辅助字段：

- `curationScore`
- `isSelected`
- `readyForBundle`
- `reviewNotes`
- `rejectionReason`
- `previewVariant`

## 3. curation-state 的职责

`curation-state.v1.json` 不是移动端直接消费的数据，而是工作台运营层状态。

它的作用：

- 把人工判断沉淀成结构化记录
- 避免每次都靠记忆重新筛选
- 为 build-bundle 提供可计算的发布边界

## 4. 当前建议边界

- source 数据长期存于 `daypalette-color-assets/source/current/`
- 工作台不再额外保留 `sample-data/` 副本
- 浏览器 localStorage 只是临时缓存，不应视为真相源
- 需要长期保存或跨机器迁移时，应导出并写回 `curation-state.v1.json`

## 5. 人工审核建议

每次进入 build 之前，至少确认：

1. 是否存在明显重复或过度相似的 palette
2. 是否有季节或场景标签不一致的条目
3. 是否有虽然好看但不适合产品当前调性的 expressive 候选
4. warning 是否已经人工确认可以接受
