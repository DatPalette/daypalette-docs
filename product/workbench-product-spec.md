# 配色运营工作台 PRD（已归档）

> 归档状态：自 2026-04-29 起，本文对应的本地 workbench 产品方案已整体归档。统一入口见 [../archive/color-operations-reset-2026-04.md](../archive/color-operations-reset-2026-04.md)。
> 使用约束：本文仅保留历史产品判断，不得作为新方案真相源。
> 本版于 2026-04-21 重写，原本目的是把 workbench 从“单页审阅器”重新定义为“围绕资产维护、专题组织、文案落地与发布检查展开的本地运营工作台”。

## 1. 文档定位

本文用于定义 `daypalette-palette-workbench` 的共享层产品判断，重点回答下面四个问题：

1. workbench 到底是什么产品，而不是什么产品。
2. App、资产仓与共享内容系统，反过来要求 workbench 成为什么样的工具。
3. workbench 应该围绕哪些对象与任务组织，而不是只围绕一个审阅页面组织。
4. 当前阶段的一等能力、状态边界与成功标准应如何收口。

本文的目标读者是：

1. 产品与设计决策者。
2. `daypalette-palette-workbench` 实现者。
3. 需要理解 workbench 边界的 AI Agent。

---

## 2. 一句话结论

**DayPalette workbench 不是单页审阅器，也不是通用 CMS；它是一个本地运行、面向单人运营的配色内容维护台，用来维护可复用色库、可发布配色盘、可策展专题、可落地文案与可消费 bundle。**

这意味着：

1. 快速审阅依然重要，但只是 workbench 的一个高频模式。
2. Collection、Locale、Release 不再是边角功能，而是产品的一等对象。
3. workbench 成功与否，不只看“卡片去留是否顺手”，还看“资产结构是否稳、发布边界是否清、App 消费是否无惊喜”。

---

## 3. 从 DayPalette 产品链路反推的事实

### 3.1 App 面向用户的是当天决策，不是内容后台

DayPalette 面向最终用户的核心任务，是快速给出当天可穿、可分享、可上卡片的配色决策。

因此：

1. App 主界面是单页、沉浸式、低负担的灵感与决策界面。
2. workbench 的职责不是模仿 App 主界面，而是为 App 提供可审核、可解释、可发布的内容资产。
3. workbench 的设计重心必须放在内容维护与发布准备，而不是模仿终端消费体验。

### 3.2 App 消费的是 bundle，不是编辑态 source

当前跨仓数据流是：

1. `daypalette-color-assets/source/current/` 是编辑态真相源。
2. `curation-state.v1.json` 是工作台运营层状态。
3. `daypalette-color-assets/bundle/current/` 是消费态输出。
4. App 只消费 bundle / rawfile 导入结果，不直接消费 source。

因此：

1. workbench 里的“通过”不等于 App 一定可消费。
2. 所有运营判断都还要经过 validate、bundle 裁剪和兼容边界确认。
3. 发布检查不能继续被定义成边角模式，而必须成为清晰的一等出口。

### 3.3 DayPalette 的真实内容对象不止 Palette

当前共享内容系统至少有五类对象：

1. `BaseColor`
2. `Palette`
3. `Collection`
4. `Locale Content`
5. `Curation State`

因此：

1. 运营人员维护的不是一串孤立的 palette 卡片。
2. 运营人员维护的是一条“原子色复用 -> 配色盘判断 -> 专题组织 -> 文案落地 -> 发布准备”的内容链路。

### 3.4 产品策略是保守、稳定、可解释，不是高频内容轰炸

当前内容系统与智能配策略都强调：

1. 安全、低翻车、场景适配。
2. 由人工审核过的候选池驱动，而不是开放式探索。
3. 只做精，不做多；可复用、可重组、可审校。

因此：

1. workbench 不应该被设计成鼓励大量录入和频繁发布的通用 CMS。
2. 它更适合被设计成一个低频高判断密度的运营维护台。

### 3.5 Pro、专题与本地化文案是真实运营工作，不是附属信息

当前产品策略明确涉及：

1. `isPro` 对付费边界的影响。
2. `Collection` 对专题表达、上新节奏和商业组织的作用。
3. 中英双语名称、描述、Hero 文案对 App 品牌气质的直接影响。

因此：

1. workbench 不能只重视 palette 视觉预览，而忽略专题、Pro 和 locale 文案。

---

## 4. 产品定义

### 4.1 它是什么

workbench 应被定义为：

1. 本地运行的运营维护台。
2. 围绕 `BaseColor / Palette / Collection / Locale / Release` 组织工作的内容工具。
3. 面向单人运营、低流程复杂度、强文件真相源约束的桌面工作环境。

### 4.2 它不是什么

当前阶段，workbench 不是：

1. 一个只有审阅队列的单页工具。
2. 一个通用原始 JSON 编辑器型后台。
3. 一个远端在线 CMS。
4. 一个 Git 图形客户端。
5. 一个多人协作和审批流系统。

### 4.3 当前目标用户

当前阶段只服务一类核心用户：

1. 单人本地运营者 / 主理人。

这意味着：

1. 不需要多人协作心智。
2. 不需要权限体系心智。
3. 不需要复杂审批链。
4. 但必须把保存、覆盖、校验、回退写清楚。

---

## 5. 运营人员的真实日常任务

如果从 App 的消费方式反推，一个运营人员日常并不是“打开工作台，扫一批卡片，然后点通过”。

更真实的工作链路是：

1. 判断今天要维护的是基础色、palette 候选、专题合集、文案，还是发布清单。
2. 新增或重组 palette 时，优先复用已有 `BaseColor`，而不是无约束新增近似颜色。
3. 为 palette 补齐场景、风格、情绪、季节、安全等级、Pro 标记等结构化字段。
4. 把相近候选放在一起比较，决定是保留、延后、淘汰，还是转入某个专题。
5. 给 palette 或 collection 写中英文命名、描述、Hero 文案，保证品牌气质一致。
6. 维护 collection 的封面、排序、成员与重复曝光控制。
7. 在发布前确认哪些 palette 真正满足 bundle 条件，哪些只是“看起来不错但还不能发”。
8. 通过校验、bundle 和导入链路，把这次内容维护安全交给 App 消费。

这条链说明：

**快速审阅只是运营工作的一段，不是整个产品。**

---

## 6. 一等对象

workbench 的第一层组织原则，应优先回答“我在维护什么对象”，而不是“我在看哪块屏幕”。

当前阶段的一等对象应至少包括：

1. `Palette Candidate`：待判断、待复核、待淘汰的候选。
2. `Palette Library`：正式资产库中的 palette。
3. `Collection`：专题与合集。
4. `Locale Content`：中英文名称、描述、Hero 文案。
5. `Release Result`：校验、publishable 清单、bundle 边界。

`BaseColor` 也是真实对象，但当前阶段可以不作为独立首屏主模式；它至少需要在 palette 与 collection 维护中可被查看、复用与追踪引用。

---

## 7. 推荐产品结构

### 7.1 总体形态

推荐形态不是“多页面 CMS”，也不是“一张无限增肥的单页工作台”，而是：

**一个桌面壳 + 五个任务模式。**

### 7.2 五个任务模式

#### A. Inbox：候选审阅台

职责：

1. 承接新候选、待复核候选、待淘汰候选。
2. 做快速保留 / 淘汰 / 对比。
3. 解决“这一批 palette 里谁该进资产库”的问题。

在这个模式里，旧版 PRD 中强调的筛选、审阅队列、对比台、详情抽屉仍然有价值，但它们只服务于 Inbox 这一个模式。

#### B. Palette Library：配色盘资产库

职责：

1. 查看所有 palette 的结构化信息。
2. 编辑标签、场景、Hero 文案、Pro 标记、安全等级。
3. 查看 palette 引用了哪些 base color、被哪些 collection 使用。
4. 处理近似重复、归档与复用问题。

#### C. Collection Planner：专题维护台

职责：

1. 维护 collection 的成员、封面、顺序、说明。
2. 控制多个专题之间的重复曝光。
3. 处理 `Citywalk 拍照 / 公园出片 / 国风新中式` 这类策展任务。
4. 管理 `isPro`、`releaseMode`、`status` 等专题级字段。

#### D. Copy Desk：文案与本地化台

职责：

1. 编辑 palette / collection 的中英文名称与说明。
2. 检查 Hero 文案是否符合 App 气质。
3. 补齐缺失 locale，避免 bundle 可导出但文案缺键。

#### E. Release Desk：发布前检查台

职责：

1. 汇总当前 publishable 集合。
2. 展示 error / warning。
3. 预览 bundle 会裁剪出哪些数据。
4. 串联 validate、build-bundle、import-bundle-to-app 的出口判断。

### 7.3 不是每个模式都必须做成独立 URL 页面

这里的“模式”强调的是任务边界，不等于必须使用复杂路由。

可接受的形态是：

1. 一个桌面壳。
2. 顶部或左侧稳定模式切换。
3. 每个模式复用相似的布局骨架，但对象与交互不同。

核心原则是：

**按任务切，不按视觉块硬切。**

---

## 8. 当前阶段的一等能力

### 8.1 审阅能力

Inbox 模式至少应支持：

1. 场景、状态、安全等级、标签、关键词筛选。
2. 候选快速保留 / 淘汰 / 待定。
3. 2 到 4 个候选的稳定对比。
4. 按需展开详情补录。

### 8.2 资产维护能力

Palette Library 至少应支持：

1. 查看 palette 的结构化字段。
2. 编辑标签、场景、安全等级、Pro 标记、Hero 相关字段。
3. 查看 palette 与 base color / collection 的关系。

### 8.3 专题维护能力

Collection Planner 至少应支持：

1. 维护 collection 成员与封面。
2. 调整专题排序与说明。
3. 查看 palette 在多个专题之间的复用与冲突。

### 8.4 文案维护能力

Copy Desk 至少应支持：

1. 中英文名称与说明补录。
2. Hero 文案检查。
3. 缺失 locale 项提示。

### 8.5 发布能力

Release Desk 至少应支持：

1. 阻塞项与 warning 的分层展示。
2. publishable palette 清单。
3. 按场景 / 专题 / 条件汇总的可导出数量。
4. 从问题回跳到对应对象继续处理。

### 8.6 保存能力

workbench 必须清楚表达：

1. 当前看到的是仓库 source、导入文件，还是浏览器会话缓存。
2. 当前是否存在未落盘修改。
3. 导入是覆盖还是 merge。
4. 导出写出的到底是 `curation-state`、source patch，还是完整 source。

---

## 9. 数据与状态边界

### 9.1 真相源不变

当前共享层只认下面这条链路：

1. `daypalette-color-assets/source/current/` 是编辑态真相源。
2. `curation-state.v1.json` 是工作台运营层状态。
3. `daypalette-color-assets/bundle/current/` 是生成后的消费态产物。
4. App 只消费 bundle，不直接承担日常策展职责。

### 9.2 必须拆开的三类状态

当前 workbench 至少有三类状态，不能继续混写成一个 badge 系统：

1. **资产生命周期**：对象本身是不是 `draft / approved / published / archived`。
2. **工作台审阅判断**：当前运营轮次里，它是 `candidate / shortlisted / approved / rejected / archived` 中的哪一种。
3. **发布准备状态**：它是否表达了“进入本轮 bundle”的意图，以及最终是否真的 publishable。

### 9.3 当前推荐语义

在不改变跨仓数据流的前提下，推荐这样理解现有字段：

1. `source/*.json` 中对象自己的 `status`：资产库生命周期。
2. `curation-state.v1.json.reviewStatus`：工作台审阅阶段，优先服务 Inbox 判断。
3. `isSelected`：是否进入本轮正式候选集。
4. `readyForBundle`：是否表达“希望进入这次 bundle”。
5. `publishable`：派生结果，不直接手填；必须由规则与校验共同算出。

### 9.4 `reviewStatus` 的边界

`reviewStatus` 不应再同时承担：

1. 资产长期生命周期。
2. 审阅队列判断。
3. 发布最终判定。

否则产品与实现都会继续混乱。

### 9.5 浏览器缓存的边界

`localStorage` 可以继续存在，但只能被视为：

1. 会话恢复缓存。
2. 临时草稿缓存。

它不应再被误用成长期真相源。

---

## 10. 当前阶段目标与非目标

### 10.1 当前阶段目标

MVP 阶段，workbench 必须解决：

1. 候选审阅与结构化判断沉淀。
2. palette 资产的基础编辑与复用检查。
3. collection 的成员、封面与排序维护。
4. 中英文内容文案的补录与缺失检查。
5. 发布前检查、publishable 清单与 bundle 出口确认。
6. 显式保存 / 导入 / 导出 / 回到仓库基线。

### 10.2 当前阶段非目标

MVP 阶段明确不做：

1. 多人协作与权限体系。
2. 复杂审批流。
3. 通用 JSON 编辑器型后台作为主入口。
4. 远端在线同步与在线 CMS。
5. Git 图形客户端式操作。
6. 运行时动态下发到 App 的独立后台链路。

---

## 11. 成功标准

workbench 是否成功，不看它像不像传统后台，而看下面这些事是否成立：

1. 运营者能否在 Inbox 模式下完成一轮高频候选审阅，而不被保存与状态语义打断。
2. 运营者能否在不离开 workbench 的前提下，完成 palette、collection 与文案的基本维护闭环。
3. `reviewStatus`、`isSelected`、`readyForBundle` 与最终 publishable 之间的关系是否足够清楚。
4. 发布前是否能明确知道这次 bundle 会产出什么、缺什么、挡在哪里。
5. App 是否继续只消费 bundle，并保持与现有兼容边界一致。
6. 用户是否不需要依赖记忆，就能回忆某个 palette 为什么被保留、为什么被淘汰、为什么还不能发。

---

## 12. 当前仍待确认的问题

当前还有三件事可以继续讨论，但不影响这版 PRD 先落地：

1. `BaseColor` 是否在当前阶段就升级为独立一等模式，还是先作为 Library / Planner 的引用视图存在。
2. Copy Desk 是否继续作为独立模式存在，还是在当前阶段先并入 Palette Library 与 Collection Planner 的编辑流。
3. Release Desk 是否在当前阶段就提供 bundle diff，还是先把 publishable 清单与阻塞项做稳。

这三个问题在没有明确结论前，都不应动摇本文最核心的产品判断：

**workbench 的本质是本地运营维护台，其中审阅只是高频模式，不再代表整个产品。**