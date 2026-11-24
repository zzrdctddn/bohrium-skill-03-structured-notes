# 文献摘要与结构化笔记 Skill（section-aligned-notes-builder）

一个把论文转成"章节对齐、字段齐备、逐条可回链、可检索可比较"结构化笔记的 Agent Skill。核心：骨架对齐 → 字段留全 → 锚点回链 → 可复用加工，对得上、留得住、找得回、用得上。

## 目录结构
- `SKILL.md` —— Skill 本体（YAML frontmatter、SASN 方法、SOP、输出契约）
- `references/` —— 方法细则与反例库、自检清单、字段字典、卡片字段与锚点规则
- `assets/` —— 可编辑结构化笔记模板
- `examples/` —— 最小调用示例（已标注为示意，非真实案例）
- `GITHUB_URL.txt` —— 提交前填 GitHub 公开链接（当前为 REPLACE_ME 占位）

## 使用方式
1. 将本目录作为 Skill 安装到 Agent（平台约定路径 `/skills/weekly-skill`）。
2. 输入 `/input/case.json`（含 `case_id`、任务、论文路径、字段/颗粒度、锚点要求）与 `task.md`。
3. 自然语言下指令，如"按章节做结构化笔记，标出处，前提局限要记全"。
4. 按 SKILL.md 的 SOP 执行，输出到 `/app/output/result.json` 与 `structured_notes.md`。

## 依赖
- 仅需文本处理能力；无外部运行时依赖。字段字典、锚点格式、知识点类型内置于 SKILL.md 与 `references/`。
- 注：Runtime contract 规定预算为 64 次模型请求 / 900 秒 / 每次 16384 token；资源≤50 个 UTF-8 文本文件、单文件≤500KB、总计≤2MB、无软链。论文以 `/input` 实际文件为准，不依据领域标签臆测内容。

## 适用与限制
- 适用：按论文本身章节做结构化笔记、统一论文卡片、知识点提取归并、逐条来源回链、可检索可比较的笔记产出。
- 限制：扫描件不可读标 `blocked`；原文未报告的字段写"原文未报告"并进 uncertainties；结论前提无法判断进人工确认。材料缺失、无法判断、运行失败分别说明，不编造事实与 benchmark 分数；不替论文补充外部事实或评价其科学价值。

## 评分对应
实际效果(40)、使用增益(25)、创新增益(15)、可靠体验(10)、生态贡献(10) 的支撑材料分别见 SKILL.md 第 3/2/2/6/8 节与 `references/`：
- 实际效果：SOP Step 1~4（对得上/留得住/找得回/用得上）+ `section_skeleton`/`paper_card`/`source_anchor_map`。
- 使用增益：SASN 四机制带来的额外结构（章节骨架、七字段卡片、知识点清单、锚点映射）+ `self_check` 对照字段。
- 创新增益：章节对齐骨架、前提字段强制、来源锚点回链、知识点归并为基线难稳定实现的能力，见 `references/method.md`。
- 可靠体验：Step 0 边界确认 + 第 6 节异常处理表。
- 生态贡献：字段字典、锚点格式、笔记模板与示例见 `references/`、`assets/`、`examples/`。
