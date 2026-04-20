# DayPalette AI 协作模型（跨仓共享）

本文是 DayPalette 四个仓库共用的 AI 协作方法论真相源。

适用仓库：

- `day_palette`（HarmonyOS 移动端）
- `daypalette-color-assets`（配色资产 source / bundle）
- `daypalette-palette-workbench`（策展工作台 Web）
- `daypalette-docs`（顶层共享文档）

> 本文只描述**通用模型与原则**。每个仓的具体落盘清单、目录结构、技术细则放在各仓内部（例如 `day_palette/docs/ai-workflow/00-AI-DEVELOPMENT-HARNESS.md`、`daypalette-palette-workbench/skills/`）。

---

## 1. 三层协作结构

任何仓库内的 AI 协作都建议按这三层组织：

| 层级 | 载体（示例） | 作用 |
|------|-------------|------|
| **Agent 宪章** | 仓内 `AGENTS.md` | 任何会话先读的短约束：角色、文档优先级、需求确认后先改文档、本仓特例。 |
| **项目级方法论** | 仓内 `docs/ai-workflow/` 或 Skill 目录 | 完整操作手册：落盘矩阵、目录结构、范式、与 Skills 映射。 |
| **工具/语言强制规则** | `.cursor/rules/*.mdc` 或 Cursor / Copilot Skill | 编辑特定后缀文件时按 glob 自动附加规则。 |

**取舍原则**：

- `AGENTS.md` 必须**短**，是 Agent 每次都会读的高频内容；超过一屏说明你应该把内容下放到第二层。
- 第二层是**完整方法论**，不要求 Agent 每次都读，但应该可以被随时引用。
- 第三层用于**自动注入约束**，避免 Agent 漏看；不要把可被 §1/§2 取代的信息硬塞这里。

---

## 2. 需求落盘的通用原则

不论在哪个仓，当用户说「需求已确认 / 定稿」时，Agent 都应**先把变更写入对应文档，再改代码**。仅改代码不落文档视为未完成。

落盘文档分两类：

### 2.1 跨仓变更 → 优先写到 `daypalette-docs`

- 业务概念、内容系统对象、状态机、命名口径
- 多仓边界、真相源、生成物
- 资产生产、策展、校验、发布的操作流程
- 跨端共享设计语言
- 跨仓提交规范

### 2.2 单仓变更 → 写到本仓对应文档

- 平台专属实现（ArkUI 组件、React 组件、脚本）
- 平台专属 UX 入口
- 单仓的构建 / 发布 / 测试细节

**冲突处理**：共享层与单仓文档冲突时，以共享层为准；单仓应**先修文档边界，再改实现**。

---

## 3. Skills 的角色

Skills（Cursor Skill / Copilot Skill）适合固化「可重复的多步流程」，例如：

- 仅更新设计令牌
- 仅补 PRD 验收标准
- 提交准备（差异审查 + commit message 草拟）
- 模块拆分与命名

**约束**：

1. Skill 内容必须**与共享层文档兼容**；冲突时以共享层为准，Skill 负责落地细节。
2. Skill 触发条件应明确（"发生 X 时使用"），避免与默认 Agent 行为重复。
3. 一个 Skill 解决一类流程，不堆叠多个无关任务。

---

## 4. Prompt 工作流通用原则

不论目标平台是 HarmonyOS / Web / Node CLI / 文档，与 AI 协作生成代码时建议遵守：

1. **拆解与分步投喂**：按 **数据层 → 基础结构 → 样式细节 → 交互动效** 的顺序逐步实现，每次对话只专注一个具体任务并验证效果。
2. **先建立上下文再写代码**：让 Agent 先阅读 PRD / 设计系统 / 架构 / 共享语言文档，再开始任何实现。
3. **不直接把整个原型/旧文件丢给 Agent**：高保真原型（HTML / Figma 导出）容易让 Agent 丢失细节；应抽取所需片段后再投喂。
4. **以文档为唯一真相源**：当 Agent 与代码或人脑记忆冲突时，以仓内文档为准；冲突时先更新文档，再写代码。
5. **每步验收**：每完成一个 Step，让 Agent 说明假设与风险，等待人类确认后再继续。

---

## 5. 各仓的具体方法论入口

| 仓库 | 入口文档 | 备注 |
|------|---------|------|
| `day_palette` | [`day_palette/docs/ai-workflow/00-AI-DEVELOPMENT-HARNESS.md`](../../day_palette/docs/ai-workflow/00-AI-DEVELOPMENT-HARNESS.md) | HarmonyOS 工程目录、ArkTS 范式、HTML→ArkUI 拆解式 prompt |
| `daypalette-palette-workbench` | [`daypalette-palette-workbench/skills/frontend-module-playbook/SKILL.md`](../../daypalette-palette-workbench/skills/frontend-module-playbook/SKILL.md) | React/TS 模块分层、组件、提交、文档规范 |
| `daypalette-color-assets` | [`daypalette-color-assets/AGENTS.md`](../../daypalette-color-assets/AGENTS.md) | 资产 source / bundle 边界与提交特例 |
| `daypalette-docs` | [`daypalette-docs/AGENTS.md`](../AGENTS.md) | 共享文档写作边界与治理原则 |

---

## 6. 与共享提交规范的关系

本文只覆盖「Agent 协作模型」；提交流程见 [`../operations/git-commit-workflow.md`](../operations/git-commit-workflow.md)，不在此重复。
