---
name: dialogue-outline-generation
display_name: 对话归档交接
display_name_en: Dialogue Outline Generation
description_zh: >-
  将当前对话按语义相关性整理为可归档的 Markdown 产物：切分为若干主题，分层落盘为问答流水与净结论，并可生成唯一一份交接文档供其他 agent 接续工作。当用户说「整理对话」「整理思路」「生成交接文档」时使用。
description_en: >-
  Archives the current conversation into Markdown by splitting it into semantic topics and persisting a layered raw log and conclusions, plus an optional single handoff document for another agent to continue the work. Trigger on "organize the conversation", "organize my thoughts", "generate a handoff document".
version: 3.1.0
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

## 执行前置（每次执行都必须做，不得跳过）

**格式权威在磁盘上，不在你的上下文里。**

1. 先读这三份模板：`assets/templates/00_handoff.template.md`、`assets/templates/01_conclusions.template.md`、`assets/templates/02_raw_log.template.md`。
2. 再读 `references/output-format.md`（产物结构契约与校验要点）与 `references/design.md`（行为边界与剔除清单）。
3. **同对话内已有的历史产物、你记忆中的旧版本，都不构成格式依据。** 只有本步读到的模板是权威——模板可能在你上一次执行之后被改过。
4. **版本一致性自检**：读磁盘上的 `SKILL.md`，比对它的 `version` 与你上下文里那份的 `version`。若不一致，**以磁盘版为准**并按新版规则执行（你上下文里的技能内容可能是本会话开始时载入的旧版，此后技能已升级）。

## 两个动作

### 动作一：整理（archive）—— 必选

把当前对话切分为若干**主题**，分层落盘为**流水**与**净结论**。

- 主题是文件内部的二级标题，**不产生独立文件**。
- 仅当两个主题之间明确无逻辑关联时才切分；不设单文件字符上限。
- **剔除技能痕迹**：本技能自身的触发、执行、回执与交互回答一律不入产物；**其他技能的使用正常保留**。判定口径与混合轮处理见 `references/design.md`。

### 动作二：交接（handoff）—— 用户选择是否执行

- 交接文档**只引用净结论**，不含流水全文，以控制下游 agent 的上下文占用。
- 每个归档批次**最多一份**交接文档（批次内唯一，不按主题各出一份）。用户选择不产出时，仅完成动作一。
- 不得默认产出，也不得静默跳过询问——询问时机见「执行步骤」第 5 步。

## 执行步骤

1. **执行前置**：按上节读取三份模板与两份参考文档。
2. **确定目标根目录**：用户显式指定 > 默认 `~/Desktop`；不存在则创建；不可创建或不可写时回退到默认根目录。详见 `references/design.md`。
3. **圈定范围**：默认整理整场对话；用户显式圈定范围时以用户为准。
4. **剔除技能痕迹并切分主题**：先按 `references/design.md` 的判据剔除技能痕迹，再切分主题；剔除后不剩内容的主题随之消失、编号重排。**若剔除后整场对话无任何可归档内容，停止并不产出任何文件，明确告知用户。**
5. **一次性交互**（主题数 >2 时）：一次问完两件事——切分方案（主题清单、各主题涵盖范围、目标路径）与是否生成交接文档；询问交接时须说明其用途：**交接文档供新 agent 或新对话窗口承接上下文、继续工作**。用户确认后再落盘。
   - 主题数 ≤2 时免去方案确认，但仍须询问是否生成交接文档。
6. **创建归档批次目录**：`<目标根目录>/dialogue-generation-file/<YYYYMMDD-HHMMSS>/`，时间戳取**执行时刻**。
7. **写 `02_raw_log.md`**，套用第 1 步读到的模板；按 `references/design.md` 的清单决定保留与剔除，并把轮次内容中的**标题按本轮偏移量落级**（偏移量 = 4 − 本轮最浅标题层级）、**行首分隔线转义**（围栏代码块内一律不动）。
8. **写 `01_conclusions.md`**，套用第 1 步读到的模板。
9. **若用户选择交接**，写 `00_handoff.md`，套用第 1 步读到的模板。
10. **落盘前自检**：逐条核对 `references/output-format.md` 的「校验要点」，不合规就修正后再落盘。
11. **汇报结果**：只给批次路径、文件清单与一行自检结论。**不得复述净结论、不得给出跟进建议、不得贴出产物正文**——那些是净结论的职责，复述会把本次执行的内容重新灌回对话上下文。

## 本技能自带资源

- `references/design.md` —— 锁定的设计决策、框架噪音与技能痕迹清单、路径回退规则、幂等与确认流程。**执行第 2、4、5、7 步前先读。**
- `references/output-format.md` —— 产物的目录结构、三个文件的职责边界、头部元信息与校验要点。
- `assets/templates/00_handoff.template.md` —— 交接文档模板。
- `assets/templates/01_conclusions.template.md` —— 净结论模板。
- `assets/templates/02_raw_log.template.md` —— 问答流水模板。

## 语言约定

生成内容默认使用当前对话所用语言；中文对话输出中文文档。
