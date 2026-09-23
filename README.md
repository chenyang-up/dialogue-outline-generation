# Dialogue Outline Generation

一个 Agent Skill：把当前对话按语义相关性整理为 Markdown 产物存档，并可生成交接文档供其他 agent 接续工作。

## 它做什么

- **整理**——把一段长对话切分为若干**主题**，分层落盘为**问答流水**与**净结论**。
- **交接**——可选地产出一份交接文档，只引用净结论，供下一个 agent 以最小上下文接续工作。

产出的目录形状是固定的：

```
~/Desktop/dialogue-generation-file/
└── 20260923-133100/          # 归档批次
    ├── 00_handoff.md         # 交接（可选）
    ├── 01_conclusions.md     # 净结论
    └── 02_raw_log.md         # 问答流水
```

## 怎么用

对话中说 **「整理对话」**、**「整理思路」** 或 **「生成交接文档」** 即可触发；也可手动调用 `/整理`。

落盘前会先给你看切分方案（主题清单与目标路径），确认后才写。主题 ≤2 个时跳过这道确认。

## 安装

把本目录放到技能加载目录下：

- 用户级：`~/.workbuddy/skills/dialogue-outline-generation/`
- 项目级：`<项目>/.workbuddy/skills/dialogue-outline-generation/`

也可以打包成 zip 上架到技能平台——`SKILL.md` 的 frontmatter 已包含平台校验所需的展示名与双语描述。

## 目录说明

| 路径 | 用途 |
|---|---|
| `SKILL.md` | 技能主文件：触发条件、两个动作、执行步骤、资源引用 |
| `CONTEXT.md` | 词汇表——本项目专有术语的权威定义 |
| `references/design.md` | 锁定的设计决策与边界规则（框架噪音清单、路径回退、幂等、确认流程） |
| `references/output-format.md` | 产物结构规范与验收要点 |
| `assets/templates/` | 三个产出模板，执行时套用 |
| `docs/adr/` | 关键取舍的决策记录 |
| `docs/agents/` | 工程 skill 的仓库配置（issue tracker、triage 标签、域文档布局） |
| `.scratch/` | issue 与 spec 的存放位置 |

## 设计取舍

几条容易被误读、刻意为之的决策，理由记在 `docs/adr/`：

- 主题留在文件内分节，**不建独立目录**（ADR-0001）
- 交接**只引用净结论**，不含流水全文（ADR-0002）
- 语义切分**故意不设字符上限**（ADR-0003）
- 流水与净结论**分层存储**（ADR-0004）
- 净结论按**内容类型**分四类、「未决」降级为条目标记（ADR-0005）

## 语言

中文对话输出中文文档；frontmatter 的双语描述仅用于平台展示。
