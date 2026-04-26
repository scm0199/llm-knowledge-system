# Consensus Matrix — Template (for `projects/[topic]/consensus-matrix.md`)

```yaml
---
id: [topic-slug]-consensus
domain: [field]
type: interpretation
source_lineage: [[topic-slug-brief]], [all sub-agent outputs]
last_updated: YYYY-MM-DD
---
```

# Consensus Matrix: [Topic]

## Method

All claims extracted from Phase 1 sub-agent findings were normalized (near-duplicates merged into canonical statements) and scored against each sub-agent independently. Status was assigned by:

| Status | Rule |
|---|---|
| `CONSENSUS` | ≥3 agents confirm, 0 contradict |
| `STRONG` | 2 agents confirm, 0 contradict |
| `DISPUTED` | ≥1 agent contradicts (regardless of confirmations) |
| `UNVERIFIED` | Only 1 source, no corroboration |
| `CONTRADICTED` | More contradictors than confirmers |

**Source-diversity override**: If all confirmations trace back to the same primary source, downgrade to `UNVERIFIED`.

## The matrix

| # | Claim | Ag.1 | Ag.2 | Ag.3 | Ag.4 | Ag.5 | Ag.6 | Ag.7 | Ag.8 | Status | Confidence |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | [Canonical claim] | ✅ | ✅ | — | ✅ | — | — | — | — | CONSENSUS | HIGH |
| 2 | [Canonical claim] | ✅ | ❌ | — | — | ✅ | — | — | — | DISPUTED | — |
| 3 | [Canonical claim] | — | — | — | ✅ | — | — | — | — | UNVERIFIED | LOW |

Legend: ✅ confirms · ❌ contradicts · — not addressed

## Thematic clusters

Group related claims into themes to reveal the structure of the topic:

### Theme A: [name]
- Claims: [#1, #4, #7]
- Overall status: [CONSENSUS / DISPUTED / MIXED]
- Notes: [what the cluster as a whole tells us]

### Theme B: [name]
- Claims: [#2, #5]
- Overall status: [...]

## Disputed findings (→ Socratic review)

For each DISPUTED or CONTRADICTED claim:

### Claim [#N]: [summary]

- **Confirming side**: [sources + their evidence]
- **Contradicting side**: [sources + their evidence]
- **Why this matters**: [implication if one side is right]

## Blind spots

[Angles or sub-questions where ZERO sub-agents returned useful findings. These are not just gaps in evidence — they are signals that the research design itself missed something.]

## Source diversity check

[Summary: are we relying on a few upstream sources repeatedly quoted, or genuinely diverse origins? Any downgrades applied?]
