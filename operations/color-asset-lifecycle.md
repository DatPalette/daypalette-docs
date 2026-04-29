# 配色资产生命周期（已归档）

> 归档状态：自 2026-04-29 起，本文描述的 `source -> workbench -> bundle -> App` 旧链路已停止使用。统一说明见 [`../archive/color-operations-reset-2026-04.md`](../archive/color-operations-reset-2026-04.md)。
> 使用约束：本文仅保留历史操作链路说明，不得作为新方案真相源。

## 1. 目标

这份文档定义 DayPalette 当前阶段最实际的一条资产操作链路：

`source 数据 -> 策展 -> 校验 -> bundle -> App 导入 -> 发版`

当前重点是把内容链路做稳，而不是追求线上动态分发。

## 2. 当前角色分工

| 环节 | 主要仓库 | 说明 |
| --- | --- | --- |
| source 存储 | `daypalette-color-assets` | 保存 `source/current/` 与 `curation-state.v1.json` |
| 策展与校验 | `daypalette-palette-workbench` | 提供 UI 与脚本 |
| App 消费 | `day_palette` | 只在需要时导入 bundle |
| 共用规则说明 | `daypalette-docs` | 记录流程、边界、设计语言 |

## 3. 日常操作顺序

### 3.1 回到移动端基线

适用场景：

- 需要撤销试验性 bundle
- 需要确认 source 与 App 当前版本一致
- 新机器首次接手时要先拉齐基线

操作：

1. 在 `daypalette-palette-workbench` 执行 `pnpm sync-app-source`
2. 该命令会把 `day_palette` 的 palette-data 同步到 `daypalette-color-assets/source/current/`
3. 不会改动工作台本地数据副本，因为工作台不再保留副本

### 3.2 策展与状态沉淀

操作位置：`daypalette-palette-workbench`

高频动作：

- 按场景切换
- 搜索与筛选
- 对比多个候选
- 标记 `reviewStatus / isSelected / readyForBundle`
- 导入 / 导出 `curation-state.v1.json`

### 3.3 发布前校验

操作：

1. 在 `daypalette-palette-workbench` 执行 `pnpm validate`
2. 校验 source 数据结构、引用关系与发布前问题
3. 若存在 error，不进入 build-bundle
4. warning 需要人工确认是否可接受

### 3.4 生成消费态 bundle

操作：

1. 执行 `pnpm build-bundle`
2. 从 `daypalette-color-assets/source/current/` 读取 source + curation-state
3. 输出到 `daypalette-color-assets/bundle/current/`

当前 bundle 规则：

- `readyForBundle = true`
- `isSelected = true`
- `reviewStatus != rejected`

### 3.5 回灌移动端

只有在明确需要让移动端消费这一版数据时，才执行：

- `pnpm import-bundle-to-app`

目标目录：

- `day_palette/entry/src/main/resources/rawfile/palette-data/`

注意：

- 这是显式动作，不应作为日常策展的自动副作用
- 回灌后应由移动端侧自己完成后续验证与版本发布

## 4. Git 提交建议

推荐提交顺序：

1. 先提交 `daypalette-color-assets` 的 source 或 bundle 变化
2. 再提交 `daypalette-palette-workbench` 的脚本或 UI 变化
3. 公共规则调整单独提交到 `daypalette-docs`

原则：

- source 变更与工具变更尽量分开
- bundle 若只是中间产物，不应强迫常驻提交
- App 导入结果只在明确需要进入发布版本时再提交
