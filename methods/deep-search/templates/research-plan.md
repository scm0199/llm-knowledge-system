# Research Plan — Template (for `projects/[topic]/brief.md` under `deep-search`)

Use this as the skeleton for `brief.md` when the `research` command selects `deep-search`.

---

```yaml
---
id: [topic-slug]
domain: [field]
type: interpretation
source_lineage: [[wiki-background-pages-pulled]]
last_updated: YYYY-MM-DD
method: deep-search
---
```

# Research Brief: [Topic Title]

## Research question
[The one question driving this project — phrased as a question, not a statement.]

## Scope
- **Time horizon**: [e.g. "last 12 months" / "since 2020" / "all-time"]
- **Geography**: [e.g. "global" / "US + EU" / "China-specific"]
- **Domain**: [e.g. "enterprise SaaS" / "academic research" / "consumer apps"]
- **Explicit exclusions**: [what is off-topic]

## Topic type
[Technical / Business / Scientific / Social / Historical / Mixed]

## Depth signal
[Overview / Deep dive / Surface scan]

## Why deep-search was chosen
[1–2 sentences — usually: factual/current-state question + cross-validation value]

## Chosen angles (5–8)

| # | Angle | Why it matters here | Search strategy |
|---|---|---|---|
| 1 | Core definition | [why] | [web / docs / papers] |
| 2 | Current state | [why] | [web recent / news] |
| 3 | Historical context | [why] | [archive / academic] |
| 4 | Competing perspectives | [why] | [forum / critic blog] |
| 5 | Practical applications | [why] | [case studies] |
| 6 | [Technical deep-dive / Statistical evidence] | [why] | [depends on type] |
| 7 | [Future outlook / Policy landscape] | [why] | [industry reports] |
| 8 | [Expert opinions / Cultural impact] | [why] | [interviews / commentary] |

## Background from wiki (pre-pulled)

### Relevant concepts
- [[concept-1]] — [one line on what it gives us]
- [[concept-2]] — [one line]

### Relevant entities
- [[entity-1]] — [one line]

### Relevant data points
- [data point + source] (from `wiki/data-points.md`)

### Known contradictions
- [any open contradictions from `wiki/overview.md#open-contradictions` that touch this topic]

## Expected outputs

All files under `projects/[topic]/`:
- `executive-summary.md`
- `deep-dive.md`
- `consensus-matrix.md`
- `socratic-review.md`
- `source-appendix.md`
- `open-questions.md`

## Success criteria
- Every key finding has `source_lineage` traceable to a cited source
- CONSENSUS-grade findings have ≥3 independent sources
- Disputed findings have both sides documented with sources
- Socratic review has flagged hidden assumptions for every CONSENSUS finding
