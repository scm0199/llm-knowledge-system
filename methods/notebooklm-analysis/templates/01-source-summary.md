# 01 Source Summary Template

One file per newly detected NotebookLM source.

## Filename

```text
[YYYY-MM-DD]-[source-slug]-[source-id-short]-01-source-summary.md
```

## Purpose

- Summarize the new source independently
- Extract key claims, concepts, terms, and reusable questions
- Preserve source-level context before it is blended into notebook-level synthesis

## Recommended Structure

```markdown
---
source: NotebookLM
notebook_id: [notebook-id]
notebook_title: [notebook-title]
source_id: [source-id]
source_title: [source-title]
processed_by: Codex / Claude Code
run_id: [run-id]
created: YYYY-MM-DD
output_type: 01-source-summary
---

# 新增来源总结：[source-title]

## 核心摘要

[150-300 字总结这份新增来源的核心内容。]

## 关键观点

- [观点 1]
- [观点 2]
- [观点 3]

## 重要概念和术语

- **[概念]**：[说明]

## 与 Notebook 主题的关系

[说明这份来源如何补充、改变或强化当前 notebook 的研究主题。]

## 可复用问题

- [后续可以继续追问的问题]

## 不确定性与注意事项

- [NotebookLM 无法确认、来源隔离不足、需要外部验证的点]
```
