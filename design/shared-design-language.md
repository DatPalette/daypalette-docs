# DayPalette 共享设计语言

## 1. 目标

这份文档定义 DayPalette 在移动端与 Web 端都应共同遵守的设计语言层。

这里定义的是：

- 品牌气质
- 语义色角色
- 跨端共享核心令牌的基线值
- 排版角色
- 布局节奏
- 动效节奏

这里不直接定义：

- ArkUI 组件实现细节
- Web CSS 组件实现细节
- 某一个页面的像素级排版

## 2. 共同设计原则

### 2.1 纸质画廊感

界面应像经过策展的纸质画廊，而不是扁平后台。

关键感受：

- 层叠纸片感
- 柔和但明确的阴影层次
- 大留白
- 低噪声、低堆砌感

### 2.2 编辑级排版

标题与正文必须分角色，而不是全站一个字体系。

建议角色：

- Display：标题、日期、场合名、强调信息
- UI：按钮、正文、标签、设置项

### 2.3 克制的高级感

共同要求：

- 小圆角
- 细边框
- 低对比分隔
- 强调留白而不是强调装饰

### 2.4 材质一致

移动端与 Web 都应遵守同一套材质逻辑：

- 白色或浅暖底卡片
- 同色系半透明毛玻璃条
- 轻模糊、轻饱和提升

### 2.5 动效有目的

动效不追求花哨，重点在：

- 配色切换时有晕染感
- 显隐、滑入、回顶遵守统一节奏
- 非关键动画要能在弱动效场景下降级

## 3. 共享语义令牌

这些角色在跨端都应保持一致：

| 角色 | 含义 |
| --- | --- |
| `bg.page` | 主画布背景 |
| `text.primary` | 主文案、重点按钮字色 |
| `text.muted` | 次要文案 |
| `border.subtle` | 卡片与按钮边框 |
| `border.hairline` | 顶栏、分隔线 |
| `surface.card` | 白底或浅底卡片 |
| `overlay.glass` | 毛玻璃条背景 |
| `fill.inverse` | 主按钮底色 |
| `text.on-inverse` | 深底按钮上的文字 |
| `palette.main` | 当前 palette 主色 |
| `palette.secondary` | 当前 palette 辅色 |
| `palette.accent` | 当前 palette 点缀色 |

### 3.1 跨端核心令牌基线值

以下数值是 DayPalette 在 HarmonyOS、Web、Android、iOS 上共享的 **Canonical Baseline**。

原则：

- 各端可以封装成自己的变量命名方式，但默认值应与这里保持一致。
- 若因平台材质、暗色模式、无障碍对比度而需要偏离，必须先在各端实现文档中注明偏离原因，并保持语义角色不变。
- 涉及按钮、卡片、顶栏/底栏、正文等基础 UI 时，默认先用这里的值，而不是临时拍脑袋定色。

| 角色 | 基线值 | 默认用途 |
| --- | --- | --- |
| `bg.page` | `#f9f8f6` | 主画布背景 |
| `text.primary` | `#1a1a1a` | 主文案、主按钮字色、选中态 |
| `text.muted` | `#8c8c8c` | 次要文案、说明文案 |
| `border.subtle` | `rgba(0, 0, 0, 0.08)` | 卡片与按钮边框 |
| `border.hairline` | `rgba(0, 0, 0, 0.03)` | 分隔线、顶栏/底栏细边 |
| `surface.card` | `#ffffff` | 白底卡片、分组容器 |
| `overlay.glass` | `rgba(249, 248, 246, 0.75)` | 毛玻璃条背景 |
| `fill.inverse` | `#1a1a1a` | 主按钮底色 |
| `text.on-inverse` | `#ffffff` | 深底按钮上的字与图标 |

这意味着：

- Web 端主按钮默认应使用 `fill.inverse` 作为底色，也就是 `#1a1a1a`。
- Web 端主按钮文字与图标默认应使用 `text.on-inverse`，也就是 `#ffffff`。
- Web 端页面主背景默认应使用 `bg.page`，也就是 `#f9f8f6`。
- Web 端毛玻璃顶栏 / 底栏默认应使用 `overlay.glass`，也就是 `rgba(249, 248, 246, 0.75)`。

### 3.2 动态配色令牌与默认占位值

`palette.main`、`palette.secondary`、`palette.accent` 是动态令牌，正常情况下来自当前激活的 palette 数据，而不是一个永远固定的品牌色。

但为了保证多端在“首屏默认态 / 空状态 / 加载占位态”下保持一致，约定默认占位值如下：

| 角色 | 默认占位值 | 说明 |
| --- | --- | --- |
| `palette.main` | `#E8D8D3` | 默认主色块 |
| `palette.secondary` | `#C5B9B6` | 默认辅色块 |
| `palette.accent` | `#5D637A` | 默认点缀色块 |

### 3.3 跨端落地规则

推荐各端都维持同一层语义命名，再映射到平台实现：

- Web：CSS 变量，例如 `--dp-bg-page`、`--dp-fill-inverse`
- HarmonyOS：ArkTS 常量或资源令牌
- Android：Compose / XML color token
- iOS：Swift token enum / asset catalog 映射

共同要求：

- 先写语义 token，再映射到组件，不要在组件里直接散落 hex。
- 共享文档负责定义语义角色与核心基线值。
- 各端仓库只补自己的实现细节、状态差异与平台专属 token。

## 4. 字体角色

建议保持同一套中英文分工：

| 角色 | 英文建议 | 中文建议 |
| --- | --- | --- |
| Display | `Cormorant Garamond` | `Noto Serif SC` |
| UI | `DM Sans` | `Noto Sans SC` |

治理规则：

- 中文 Display 不使用 italic
- 标题、正文、标签不要混成一个体系
- 新仓库若因平台限制无法完全复用字体，至少要保持“衬线标题 + 无衬线界面”的角色关系

## 5. 布局与动效节奏

建议跨端保持这些节奏：

- 主要水平留白以 `24px` 等价网格为基准
- 主色 / 色卡 / 标签切换时长优先用 `0.6s`
- 面板滑入滑出优先用 `0.4s`
- 标准曲线优先用 `cubic-bezier(0.16, 1, 0.3, 1)`

## 6. 治理建议

共享设计语言应以这里为准；各仓库只维护自己的实现映射。

这里除了设计原则外，也负责维护跨端共享核心 token 的默认基线值；后续新增 Web、Android、iOS 时，都应先对齐 §3 的角色与数值，再在各端仓库中完成平台映射。

推荐分层：

1. `daypalette-docs/design/`：共享设计原则与语义层
2. `day_palette/docs/design/`：ArkUI 页面、组件、令牌实现
3. Web / 运营侧实现仓：CSS 变量、布局与组件实现（旧 `daypalette-palette-workbench/src/` 路径已归档，未来以新方案文档为准）

如果未来某一端的实现偏离共享语言，应优先先修正这里的原则文档，再决定是否接受偏离。
