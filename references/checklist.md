# 结构化笔记自检清单（逐项打钩）

> 以"复核者"身份独立执行，不与抽取阶段合并。每项不通过须回退对应步骤并在 `change_log` 记录。

## 对得上（Section-Aligned Skeleton）
- [ ] 骨架来自论文本身章节、编号、层级与论证顺序
- [ ] 未套用通用"背景/方法/结果/结论"模板
- [ ] 每节含目的、方法、结果、结论
- [ ] 每节有承接关系（transition_from/to）
- [ ] 章节切分依据可复核，存疑标 section_uncertain

## 留得住（Paper Card Fields）
- [ ] 研究问题已记录
- [ ] 数据已记录
- [ ] 方法与**方法成立条件**分开记录
- [ ] 主要发现已记录，数值与原文逐字一致
- [ ] 局限已记录（原文无则写"原文未报告"）
- [ ] 让结论成立的前提条件已记录
- [ ] 变量、方法条件与原文逐字一致，无编造

## 找得回（Source Anchors）
- [ ] 每条关键笔记有 source_anchor
- [ ] 锚点含章节+段落+页码+图表/公式编号+原文依据
- [ ] 锚点精确到段落或图表，非笼统章节
- [ ] 一条结论一个锚点，无缺漏
- [ ] 无锚点的结论未保留在笔记中

## 用得上（Reusable Knowledge）
- [ ] 知识点拆成条目（概念/变量/方法/结论）
- [ ] 术语、缩写、同义表述已登记并归并
- [ ] 条目无重复、无互相矛盾
- [ ] 字段统一，可检索、可比较、可继续加工
- [ ] 矛盾条目进 human_review_items，未擅自择一

## 结构与边界
- [ ] artifact 含 section_skeleton/section_notes/paper_card/knowledge_points
- [ ] term_registry、source_anchor_map 齐全
- [ ] status 诚实（success/partial/blocked）
- [ ] partial/blocked 写明原因与缺口
- [ ] 原文未报告字段进 uncertainties，未编造外部事实
- [ ] 无编造数值、benchmark 分数或软件状态
- [ ] 独立核对完成，所有改动均在 change_log
