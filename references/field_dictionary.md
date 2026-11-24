# 字段字典：论文卡片、锚点与知识点

本文件是 SASN 机制二、三、四的字段级细则，供 Agent 执行时查表。

## 1. 论文卡片七类字段（paper_card）
| 字段 | 含义 | 填写规则 |
|---|---|---|
| research_question | 研究问题 | 论文试图回答的核心问题，逐字或忠实转述 |
| data | 数据 | 数据集/样本/来源，含规模（原文有则记） |
| method | 方法 | 提出的方法/模型/流程 |
| method_conditions | 方法成立条件 | 假设、前提、适用范围、超参设置；与 method 分开 |
| main_findings | 主要发现 | 结果条目，数值与原文逐字一致 |
| limitations | 局限 | 论文自述或可从原文直接读出的限制；原文无则写"原文未报告" |
| conclusion_preconditions | 结论前提 | 让结论成立的条件（"在什么条件下结论成立"）；无法判断标 precondition_uncertain |

## 2. 章节笔记字段（section_notes）
- `section_id`：对应 section_skeleton 的 id。
- `purpose`：该节目的（为什么有这一节）。
- `method`：该节采用的方法。
- `result`：该节给出的结果。
- `conclusion`：该节结论。
- `source_anchor`：该节锚点（见下）。

## 3. 来源锚点格式（source_anchor / source_anchor_map）
- 要素：`section` + `paragraph` + `page` + `figure_table` + `basis`（原文依据）。
- `note_id`：笔记条目标识（如 `S2-result`），便于回链。
- `claim`：必须是笔记中实际论断或数值，不含概括性空话。
- `figure_table`：涉及图表时填图号表号（如 `表1`），否则留空。
- 粒度原则：关键结论精确到段落或图表；一般笔记精确到节即可，但必须可复核。

## 4. 知识点类型（knowledge_points[].type）
| 类型 | 说明 |
|---|---|
| concept | 重要概念/术语 |
| variable | 关键变量/指标 |
| method | 方法/算法/流程 |
| finding | 结论性发现 |

## 5. 术语登记（term_registry）
- `term`：原文术语（全称）。
- `abbreviation`：缩写（如有）。
- `aliases`：同义表述数组。
- `normalized`：归一后的标准条目名，供检索与比较。
- `note`：消歧说明；矛盾进 human_review_items。

## 6. 不得做的事
- 不替论文补充外部事实、不评价科学价值。
- 不把"方法"与"方法成立条件"混写。
- 不编造数值、benchmark 分数、软件状态。
- 原文未报告的字段如实标注，不静默省略。
