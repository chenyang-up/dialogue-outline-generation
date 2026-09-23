---
name: dialogue-outline-generation
display_name: 对话归档交接
display_name_en: Dialogue Outline Generation
description_zh: >-
  将当前对话按语义相关性整理为可归档的 Markdown 产物：切分为若干主题，分层落盘为问答流水与净结论，并可生成唯一一份交接文档供其他 agent 接续工作。当用户说「整理对话」「整理思路」「生成交接文档」时使用。
description_en: >-
  Archives the current conversation into Markdown by splitting it into semantic topics and persisting a layered raw log and conclusions, plus an optional single handoff document for another agent to continue the work. Trigger on "organize the conversation", "organize my thoughts", "generate a handoff document".
version: 2.0.0
description: >-
  整理对话思路：将当前会话按语义相关性切分为若干主题并归档为 Markdown 文档，并可生成交接文档供其他 agent 接续工作。当用户输入「整理对话」「整理思路」「生成交接文档」等指令，或请求将对话保存为结构化文档时触发。
agent_created: true
---

# Dialogue Outline Generation（对话大纲生成）

将当前对话按语义相关性整理为 Markdown 产物存档，并可生成交接文档供其他 agent 接续处理。包含两个独立动作：**整理（archive）** 与 **交接（handoff）**。

术语以仓库根目录 `CONTEXT.md` 为准，不要使用它列出的 `_Avoid_` 同义词。

## 何时触发

- 用户说「整理对话」「整理思路」「把对话整理成文档」「生成交接文档」「存档」等。
- 用户希望在长对话结束后沉淀思路、或把当前进度交给另一个 agent 继续。
- 手动调用：`/整理`

## 两个动作

### 动作一：整理（archive）—— 必选

把当前对话切分为若干**主题**，分层落盘为**流水**与**净结论**。

- 主题是文件内部的二级标题，**不产生独立文件**。
- 仅当两个主题之间明确无逻辑关联时才切分；不设单文件字符上限。

### 动作二：交接（handoff）—— 用户选择是否执行

整理完成后**主动询问**用户是否生成交接文档。

- 交接文档**只引用净结论**，不含流水全文，以控制下游 agent 的上下文占用。
- 全局**只产出一份**交接文档。用户选择不产出时，仅完成动作一。

## 执行步骤

1. **确定目标根目录**：用户显式指定 > 默认 `~/Desktop`；不存在则创建；不可创建或不可写时回退到默认根目录。详见 `references/design.md`。
2. **圈定范围**：默认整理整场对话；用户显式圈定范围时以用户为准。
3. **展示切分方案**：列出主题清单、各主题涵盖范围与目标路径，按 `references/design.md` 的确认流程等待用户确认。
4. **创建归档批次目录**：`<目标根目录>/dialogue-generation-file/<YYYYMMDD-HHMMSS>/`，时间戳取**执行时刻**。
5. **写 `02_raw_log.md`**，套用 `assets/templates/02_raw_log.template.md`；按 `references/design.md` 的框架噪音清单决定保留与剔除。
6. **写 `01_conclusions.md`**，套用 `assets/templates/01_conclusions.template.md`。
7. **若用户选择交接**，写 `00_handoff.md`，套用 `assets/templates/00_handoff.template.md`。
8. **汇报结果**：给出归档批次路径与文件清单。

## 本技能自带资源

- `references/design.md` —— 锁定的设计决策、框架噪音清单、路径回退规则、幂等与确认流程。**执行第 1、3、5 步前先读。**
- `references/output-format.md` —— 产物的目录结构、三个文件的职责边界与命名规则。
- `assets/templates/00_handoff.template.md` —— 交接文档模板。
- `assets/templates/01_conclusions.template.md` —— 净结论模板。
- `assets/templates/02_raw_log.template.md` —— 问答流水模板。

## 语言约定

生成内容默认使用当前对话所用语言；中文对话输出中文文档。
