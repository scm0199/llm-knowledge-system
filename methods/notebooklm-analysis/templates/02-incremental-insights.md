# 02 Incremental Insights Template

One file per update batch.

## Filename

```text
[YYYY-MM-DD]-[source-or-batch-slug]-02-incremental-insights.md
```

## Purpose

- Compare new sources against the existing notebook context
- Identify what the update adds, changes, strengthens, or contradicts
- Produce synthesis rather than simple summary

## Recommended Structure

```markdown
---
source: NotebookLM
notebook_id: [notebook-id]
notebook_title: [notebook-title]
processed_by: Codex / Claude Code
run_id: [run-id]
created: YYYY-MM-DD
output_type: 02-incremental-insights
---

# 增量洞察

## 本次新增来源

- [source-title]

## 新增洞察

- [新增内容带来的新观点]

## 被强化的既有主题

- [被新增来源强化的旧主题]

## 被修正或挑战的判断

- [新增来源对原有判断的修正、冲突或限定]

## 可沉淀的方法论

- [可以复用到后续分析的方法、原则、框架]

## 后续假设

- [值得验证的推论或假设]
```
