# Git 提交工作流（跨仓共享版）

本文是 DayPalette 四个仓库共同遵守的 Git 提交规范，供 Cursor / Copilot / Codex / 手动协作者共用。

适用仓库：

- `day_palette`
- `daypalette-color-assets`
- `daypalette-palette-workbench`
- `daypalette-docs`

> 各仓自身的 `AGENTS.md` 或 Cursor / Skill 规则可以追加**仓库特例**，但不得与本文相冲突；冲突时以本文为准。

---

## 1. 提交前必看

每次提交前都执行并阅读：

1. `git status`
2. `git diff`
3. `git diff --cached`

确认三件事：

- 本次提交范围与意图是否一致。
- 是否混入无关文件、调试残留、IDE 私有状态、构建产物、密钥或证书。
- 暂存区与工作区是否符合预期。

未跟踪文件先判断该提交还是该 `.gitignore`，**默认不提交**：构建产物、缓存、签名、密钥、证书、个人本地配置。

---

## 2. 按可独立回滚拆分 commit

每个 commit 必须能被**单独理解、单独回滚、单独复查**。

通用拆分原则：

- 文档与代码分开。
- 新功能与修复分开。
- UI 调整与逻辑重构分开。
- 配置 / 工具链改动与业务实现分开。
- 一次提交只表达一个主要意图。

---

## 3. 提交信息格式

DayPalette 内部使用两种风格，**取决于仓库**：

### 3.1 默认风格：`emoji + TYPE: 中文描述`

适用于：`day_palette`、`daypalette-color-assets`、`daypalette-docs`。

格式：

```text
emoji + TYPE: 中文描述
```

类型表：

| 前缀 | 用途 |
|------|------|
| `📦 NEW:` | 新增功能 / 新增模块 / 全新内容 |
| `👌 IMPROVE:` | 改进 / 重构 / 样式优化 / 性能优化 / 文案微调 / 配置整理 |
| `🐛 FIX:` | 缺陷修复 / 回归修复 / 边界条件修正 |
| `📖 DOC:` | 文档 / PRD / 设计说明 / 注释规范 |
| `🚀 RELEASE:` | 发版 / 版本号 / 发布流程 / 构建产物清单 |
| `🤖 TEST:` | 测试相关（新增测试 / 修测试 / 测试基建） |
| `‼️ BREAKING:` | 破坏性变更（接口、目录结构、旧流程不兼容） |

示例：

- `📦 NEW: 新增春日通勤专题配色`
- `👌 IMPROVE: 优化今日页三个入口按钮布局`
- `🐛 FIX: 修复昨日快照跨多天误判问题`
- `📖 DOC: 提升配色资产战略主文档到共享层`

### 3.2 工作台风格：`TYPE: 中文描述`（无 emoji）

适用于：`daypalette-palette-workbench`，由其内部 Skill `frontend-module-playbook/references/commit-workflow.md` 定义。

类型枚举与默认风格保持一致（`NEW` / `IMPROVE` / `FIX` / `DOC` / `RELEASE` / `TEST` / `BREAKING`），仅去掉 emoji 前缀。

> 工作台仓的细则以其 Skill 为准；本文只声明跨仓边界。

### 3.3 共同要求

- 类型前缀统一**大写**。
- 主题行控制在约 50 字以内，陈述「做了什么」。
- 描述统一使用中文，不在同一仓库内混用中英文风格。
- 若需要正文，空一行后写：动机、影响面、与文档/PRD 的对应关系。

---

## 4. 暂存纪律

- 优先**按文件或按 hunk** 有选择地 `git add`，避免 `git add .` 把无关改动一并带入。
- 提交前再扫一遍 diff，移除 `console.log`、临时代码、注释掉的废弃逻辑、个人路径。
- 不提交密钥 / token / 私有证书 / 测试脏数据。
- 不为了省事直接提交无关格式化结果。

---

## 5. 执行边界

- 只有用户**明确要求**「提交 / commit / 提代码」时，才执行 `git add` / `git commit`。
- 默认**不 push**，除非用户明确要求。
- 默认**不 amend 已推送提交**，除非用户明确要求并确认影响。

---

## 6. 跨仓变更协作

很多内容动作会同时跨多个仓（例如新增一个专题配色 → assets 改 source → workbench 改校验 → day_palette 导入 bundle → daypalette-docs 更新流程说明）。

对这种跨仓提交，约定：

1. **提交顺序**：按 `daypalette-color-assets → daypalette-palette-workbench → day_palette → daypalette-docs` 推进，确保上游真相源先就位、消费方再消费、文档最后收口。
2. **共同主题**：每个仓的 commit 主题里使用**相同的中文短语**作为前缀或主词（例如「春日通勤专题」），便于事后跨仓串联。
3. **不要同 commit 混仓**：每个仓单独 commit；任何"跨仓单一 PR"应在各仓内分别表达。

跨仓主题示例：

- `daypalette-color-assets`：`📦 NEW: 春日通勤专题 · 新增 source 配色`
- `daypalette-palette-workbench`：`IMPROVE: 春日通勤专题 · 校验脚本兼容新标签`
- `day_palette`：`📦 NEW: 春日通勤专题 · 导入 bundle 与 catalog`
- `daypalette-docs`：`📖 DOC: 春日通勤专题 · 补充资产生命周期说明`

---

## 7. 各仓特例速查

| 仓库 | 风格 | 主要特例 |
|------|------|----------|
| `day_palette` | emoji 风格 | 同时改 `docs/` 与 `entry/` 时默认**先文档、后代码**；不提交 `build/` / `.hvigor/` / `oh_modules/` / 签名与证书 |
| `daypalette-color-assets` | emoji 风格 | `source/` 与 `bundle/` 必须**分开提交**（先 source 后 bundle）；`bundle/` 视为生成物，由脚本统一更新；`changelog/` 条目单独 commit |
| `daypalette-palette-workbench` | 无 emoji 风格 | 由 `frontend-module-playbook` Skill 管理；不提交 `dist/` / `node_modules/` / `.vite/`；`scripts/` 与 `src/` 改动建议分开 |
| `daypalette-docs` | emoji 风格 | 单仓改动直接用 `📖 DOC:`；从子仓"提升上来"的内容，commit 正文里注明来源路径 |

---

## 8. 与各仓本地规则的关系

- 本文是跨仓共识层。各仓 `AGENTS.md` 应只保留**特例**与指针，不再重复正文。
- 若某仓存在更细的工具规则（例如 Cursor `.cursor/rules/git-commit-workflow.mdc` 或 Skill 的 `commit-workflow.md`），其内容必须与本文兼容；冲突时以本文为准，工具规则负责落地细节。
