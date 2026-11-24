---
name: section-aligned-notes-builder
description: 把真实论文转成"章节对齐、字段齐备、逐条可回链、可检索可比较"的结构化笔记，而非通篇压缩摘要。当用户要求"给这篇论文做结构化笔记""按章节总结目的方法结果结论""填论文卡片""提炼研究问题/数据/方法条件/主要发现/局限及前提""每条笔记标出处""做成可复用可比较的笔记"时使用。输出含章节对齐骨架、章节笔记、论文卡片、知识点清单、来源锚点与自检结果。
version: 1.0.0
license: MIT
---

# 文献摘要与结构化笔记（Section-Aligned Structured Notes）

> 面对真实论文，把"做笔记"从"读完后写一段摘要"变成**可回链、可比较、可继续加工的笔记工程**。核心不是把论文压缩得多短，而是让每条笔记都能回答："它对应论文本身的哪一节哪一段？让结论成立的前提和局限记下来了吗？下一次写作还能直接拿来用吗？"

## 1. 用途与适用边界

**解决什么**：基础 Agent 做论文笔记的典型失败——套用"背景/方法/结果/结论"通用模板而脱离论文自身章节论证顺序；只记结论不记让结论成立的条件与局限；出处笼统写"第 3 节"无法回查；术语缩写与同义表述重复或互相矛盾；笔记写成散文，无法检索、比较或继续加工。本 Skill 用"**骨架对齐 → 字段留全 → 锚点回链 → 可复用加工**"四段流水线消除这些失败，前提与局限必须记录，出处必须可定位。

**触发场景**：按论文本身章节做结构化笔记；填统一论文卡片（研究问题/数据/方法条件/主要发现/局限及前提）；知识点（概念、变量、方法、结论）拆分与归并；每条笔记回链章节段落页码图表；产出可检索、可比较、可继续写作的笔记。

**不该用**：用户只要"三句话讲清这篇论文"的极简摘要时（用普通摘要）；要逐字翻译或版面解析时（转用文献翻译/文档解析 Skill）；原文不可读或残缺且不允许标注时（应报告缺口，不臆造内容）。本 Skill 记录论文已写的内容，不替论文补充外部事实或评价其科学价值。

**输入**：`/input/case.json`（含 `case_id`、任务说明、论文材料或 PDF 路径、需要的笔记字段/颗粒度、锚点要求）与 `task.md`。论文以实际文件为准，不依据领域标签臆测内容。**输出**：`/app/output/result.json`（字段见第 4 节）+ 可编辑的 `structured_notes.md`。

## 2. 核心方法（SASN）

本 Skill 的方法名为 **SASN = Section-Aligned Structured Notes（章节对齐的结构化笔记）**，四个不可跳过的机制，一一对应题面"对得上 / 留得住 / 找得回 / 用得上"：

1. **对得上 —— 章节对齐骨架（Section-Aligned Skeleton）**：先抽取论文本身的章节与论证顺序，生成 `section_skeleton`；笔记骨架按论文自身章节展开，**禁止套用通用摘要模板**。每节记录其目的、方法、结果、结论及与相邻节的承接关系；章节切分可被独立复核。
2. **留得住 —— 论文卡片字段化（Paper Card Fields）**：用统一字段记录研究问题、数据、方法、方法成立的条件、主要发现、局限及**让结论成立的前提条件**。前提与局限是硬字段，不得因"摘要没写"而省略——原文确有则记，原文未写明则在 `uncertainties` 说明"原文未报告"。
3. **找得回 —— 来源锚点（Source Anchors）**：每条关键笔记带 `source_anchor`（章节 + 段落 + 页码 + 图表/公式编号 + 原文依据）；锚点粒度与可核验性匹配，关键结论精确到段落或图表，不笼统写"第 X 节"。回链支持后续核验与更新。
4. **用得上 —— 可检索可比较可加工（Reusable Knowledge）**：知识点拆成条目（概念、变量、方法、结论），处理术语、缩写与同义表述并归并，避免条目重复或矛盾；字段统一使同领域多篇论文可并排比较，输出结构化数据便于检索与继续写作。

贯穿一条铁律：**抽取与复核分离**——先完成笔记草稿，再以"复核者"身份独立执行第 5 节自检，两遍不可合并；每次修正写入 `change_log`。

## 3. 工作流（SOP）

严格按顺序执行，每步都有检查点；检查点不过则回退该步，不得带病推进。

**Step 0 · 输入解析与边界确认**
- 读 `case.json` 与 `task.md`，提取：论文路径、需要的笔记字段与颗粒度、锚点要求、明确禁止事项。
- 确认论文可读（有文字层或可 OCR）；不可读则 `status=blocked` 说明，不臆造内容。
- 检查点：范围与字段明确？论文可读？缺口登记 `uncertainties`。

**Step 1 · 章节对齐骨架（对得上）**
- 抽取论文本身的标题、摘要、章节层级与编号、论证顺序，生成 `section_skeleton`（section_id / level / title / page / 承接关系）。
- 检查点：骨架是否来自论文本身而非通用模板？章节切分与编号是否可复核？存疑标 `section_uncertain`。

**Step 2 · 章节笔记与论文卡片（留得住）**
- 逐节写章节笔记：目的、方法、结果、结论、与相邻节承接。
- 填论文卡片：研究问题、数据、方法、方法条件、主要发现、局限、结论前提。
- 检查点：七类字段是否齐全？让结论成立的前提与局限是否记录（原文无则写"原文未报告"并进 uncertainties）？数值与原文逐字一致？

**Step 3 · 知识点提取与归并（用得上）**
- 把重要概念、变量、方法、结论拆成 `knowledge_points` 条目；建立术语/缩写/同义登记表，归并重复条目，消解矛盾。
- 输出结构化、可检索、可比较的字段。
- 检查点：条目是否无重复、无互相矛盾？缩写与同义是否登记？字段是否统一可并排比较？

**Step 4 · 来源锚点与译法留痕式依据（找得回）**
- 为每条关键笔记写 `source_anchor`（章节+段落+页码+图表/公式编号+原文依据），汇总 `source_anchor_map`。
- 检查点：关键结论是否逐条回链？锚点粒度是否精确到段落/图表而非笼统章节？无锚点的结论不得保留。

**Step 5 · 独立复核（与抽取分离）**
- 以复核者身份逐项走 `references/checklist.md`；重点复核四类关键问题；发现错误回退对应步骤并写 `change_log`。
- 检查点：自检表全部通过，或未通过项均有 `change_log` 与处置说明。

**Step 6 · 输出**
- 写 `structured_notes.md`（可编辑结构化笔记）+ `result.json`（结构化结果，含 `case_id` 与全部输出契约字段）。

## 4. 输出契约（result.json）

```json
{
  "case_id": "<原样带回>",
  "status": "success | partial | blocked",
  "artifact": {
    "section_skeleton": [
      {"section_id": "S2", "level": 1, "number": "2", "title": "<章节标题>", "page": 3, "transition_from": "S1", "transition_to": "S3"}
    ],
    "section_notes": [
      {"section_id": "S2", "purpose": "<目的>", "method": "<方法>", "result": "<结果>", "conclusion": "<结论>", "source_anchor": {"section": "S2", "paragraph": 1, "page": 3}}
    ],
    "paper_card": {
      "research_question": "...", "data": "...", "method": "...", "method_conditions": "...",
      "main_findings": ["..."], "limitations": ["..."], "conclusion_preconditions": ["..."]
    },
    "knowledge_points": [
      {"kp_id": "K1", "term": "...", "type": "concept|variable|method|finding", "definition": "...", "aliases": ["..."], "source_anchor": {"section": "S2", "page": 3}}
    ]
  },
  "term_registry": [{"term": "...", "abbreviation": "...", "aliases": ["..."], "normalized": "...", "note": "..."}],
  "source_anchor_map": [
    {"note_id": "S2-result", "claim": "<笔记中的论断/数值>", "section": "S2", "paragraph": 2, "page": 3, "figure_table": "表1", "basis": "<原文依据>"}
  ],
  "uncertainties": ["..."],
  "human_review_items": ["..."],
  "change_log": [{"step": 2, "action": "补充结论前提条件字段", "reason": "初版遗漏局限的前提"}],
  "self_check": {"skeleton_aligned": true, "fields_complete": true, "anchors_valid": true, "reusable": true}
}
```

字段原则：`status` 诚实反映完成情况；`partial` 写清 `uncertainties` 与缺口；`blocked` 说明拦截原因；原文未报告的字段标"原文未报告"并进 `uncertainties`，**不得编造数据、数值、benchmark 分数或软件状态**。

## 5. 质量自检清单

执行前完整读取 `references/checklist.md`，逐项打钩。关键硬项：

- [ ] 骨架来自论文本身章节与论证顺序，非通用模板
- [ ] 每节含目的/方法/结果/结论及承接关系
- [ ] 论文卡片七类字段齐全（问题/数据/方法/条件/发现/局限/前提）
- [ ] 让结论成立的前提条件与局限已记录（原文无则标"原文未报告"）
- [ ] 数值、变量、方法条件与原文逐字一致
- [ ] 每条关键笔记有 source_anchor，精确到段落/图表/页码
- [ ] 知识点条目无重复、无矛盾，术语缩写同义已归并
- [ ] 字段统一，可检索、可比较、可继续加工
- [ ] 无编造内容、数值、benchmark 分数或软件状态
- [ ] 独立核对完成，改动均在 change_log

## 6. 异常处理与边界

| 情况 | 处理 |
|---|---|
| 论文无文字层/扫描件 | 可 OCR 则走 OCR 并校验；不可读则 `status=blocked`，不臆造内容 |
| 原文未报告某字段（如局限） | 写"原文未报告"并进 uncertainties，不补造外部信息 |
| 章节编号跳跃/层级含混 | 标 `section_uncertain`，按可见结构建骨架并说明 |
| 双栏错序/图表公式缺失 | 标注受影响笔记，锚点精确到可见位置，缺失处进 uncertainties |
| 超长论文 | 按章节分块处理，逐块写骨架与锚点，不丢节 |
| 非中文论文 | 保留原文术语与缩写，笔记语言按任务要求；术语登记双语 |
| 同义/缩写冲突 | 在 term_registry 归并并消解，矛盾处进 human_review_items |
| 结论前提无法判断 | 标 `precondition_uncertain`，进 human_review_items，不写成确定前提 |
| 权限受限文档 | `status=blocked`，说明权限拦截，不绕过 |

## 7. 最小调用示例（示意，非真实案例）

**输入**（`case.json` 片段）：
```json
{"case_id":"demo-001","task":"按论文本身章节做结构化笔记，包含研究问题/数据/方法条件/主要发现/局限及前提，每条标章节页码","granularity":"section","need_fields":["research_question","data","method_conditions","findings","limitations","preconditions"],"paper":"/input/materials/demo_paper.pdf"}
```
**调用**：加载本 Skill 后，用户说"用 section-aligned-notes-builder 给这篇论文做结构化笔记，按章节、标出处、前提局限要记全"。

**示意输出**（节选）：
```json
{"case_id":"demo-001","status":"success","artifact":{"section_skeleton":[{"section_id":"S2","level":1,"number":"2","title":"Method","page":2}],"section_notes":[{"section_id":"S2","purpose":"（示意）","method":"（示意）","result":"（示意）","conclusion":"（示意）"}],"paper_card":{"research_question":"（示意）","data":"（示意）","method_conditions":"（示意）","main_findings":["（示意）"],"limitations":["原文未报告"],"conclusion_preconditions":["（示意）"]}},"source_anchor_map":[{"note_id":"S2-result","claim":"（示意结论）","section":"S2","paragraph":2,"page":3,"basis":"原文段落"}],"self_check":{"skeleton_aligned":true,"fields_complete":true,"anchors_valid":true,"reusable":true}}
```
> 上述为演示接口用的虚构示例，不代表任何真实案例结果。

## 8. 资源清单

- `references/method.md` —— SASN 四机制细则与反例库
- `references/checklist.md` —— 覆盖四类关键问题的逐项自检清单
- `references/schema.json` —— `result.json` 字段字典（JSON Schema draft-07）
- `references/field_dictionary.md` —— 论文卡片字段字典、锚点格式与知识点类型
- `assets/structured_notes_template.md` —— 可编辑结构化笔记模板
- `examples/` —— 最小调用示例（已标注为示意，非真实案例）
- `README.md` —— 目录结构、使用方式、依赖与评分对应说明
- `GITHUB_URL.txt` —— 提交前替换占位链接
