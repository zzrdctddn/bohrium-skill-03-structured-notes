# 示例说明（示意，非真实案例）

本目录提供一组最小调用示例，仅用于说明输入输出接口与完整使用流程，不构成任何真实案例结果或正式答案。

- `example_input.json`：虚构输入，含 `case_id` 与 `note`（注明"演示接口用，非官方案例"）。
- `example_output.json`：虚构输出，含 `case_id`、`status`、主产物 `artifact`（章节骨架+章节笔记+论文卡片+知识点）、`term_registry`、`source_anchor_map`、`uncertainties`、`human_review_items`、`change_log`、`self_check`。笔记内容、数值与锚点均为占位示意。

## 调用流程
1. 加载 `section-aligned-notes-builder` Skill。
2. 按 `example_input.json` 的形态准备 `/input/case.json` 与 `task.md`（真实运行时论文为实际文件）。
3. 自然语言下指令，如"按章节做结构化笔记，标出处，前提局限要记全"。
4. 按 SKILL.md 的 SOP 执行，产出 `result.json` 与 `structured_notes.md`。

## 复用提示
- 示例中的 `granularity`、`need_fields`、`language` 可替换为你自己的任务需求；字段字典与锚点格式见 `references/field_dictionary.md`。
- 不要把示例中的虚构笔记、数值或锚点当作真实笔记结果；正式结果必须来自对真实论文的实际抽取与复核。
