# 04 Question Backlog Template

One file per update batch.

## Filename

```text
[YYYY-MM-DD]-[source-or-batch-slug]-04-question-backlog.md
```

## Purpose

- Turn the update into a prioritized queue of future research questions
- Preserve productive uncertainty instead of forcing premature conclusions
- Route follow-up questions to suitable methods when possible

## Recommended Structure

```markdown
---
source: NotebookLM
notebook_id: [notebook-id]
notebook_title: [notebook-title]
processed_by: Codex / Claude Code
run_id: [run-id]
created: YYYY-MM-DD
output_type: 04-question-backlog
---

# 问题队列

## P0 立即追问

- **[问题]**
  - 重要性：[为什么必须优先处理]
  - 推荐方法：[query / lens-research / deep-search / notebooklm-analysis]

## P1 值得深入

- **[问题]**
  - 重要性：[为什么值得研究]
  - 推荐方法：[query / lens-research / deep-search / notebooklm-analysis]

## P2 长期观察

- **[问题]**
  - 重要性：[为什么适合长期跟踪]
  - 推荐方法：[query / lens-research / deep-search / notebooklm-analysis]

## 需要外部验证的问题

- [需要事实核查、市场数据、官方资料或多来源交叉验证的问题]

## 可转为研究项目的问题

- [适合后续用 research 命令展开的问题]
```
