# Lens Research — Method Command Center

A structured deep-analysis method. Takes ONE question and forces it through six fixed lenses to produce multi-angle understanding.

This is **one tool** in `methods/`. See `methods/index.md` for when to pick this vs alternatives (e.g. `deep-search` for fact-heavy parallel investigation).

This file is a **template**. Do NOT write specific research questions here — each project lives in `projects/[topic-name]/brief.md`.

## When to use `lens-research`

Use this method when the question is interpretive: "how should we think about X?", "what's really going on with Y?". It's best for topics where depth-through-multiple-perspectives matters more than breadth-of-sources.

Pick `deep-search` instead if the question is factual/current-state ("what's the latest on X?", "what are the numbers?") and demands web-heavy cross-validation.

## Node Map

Every node below is a knowledge file. Read the relevant ones before executing.

### Methodology
- [[research-frameworks]] — how to approach different types of questions
- [[source-evaluation]] — criteria for judging if a source is worth trusting
- [[synthesis-rules]] — how to combine findings across lenses without flattening
- [[contradiction-protocol]] — what to do when sources disagree. this is where real insight hides

### Lenses (the core engine)
- [[technical]] — how does it work mechanically? what do the numbers actually say?
- [[economic]] — follow the money. who pays, who profits, what markets are affected?
- [[historical]] — what patterns repeat? what's been tried before? what context is everyone ignoring?
- [[geopolitical]] — which countries, which power dynamics, which alliances?
- [[contrarian]] — what if the consensus is wrong? who benefits from the current narrative?
- [[first-principles]] — forget everything you think you know. rebuild from basics.

### Project Outputs (in `projects/[topic]/`)
- `brief.md` — research question, scope, background from wiki
- `executive-summary.md` — the final synthesis. 500 words max
- `deep-dive.md` — the full analysis organized by lens, with cross-references
- `key-players.md` — people, organizations, countries that matter most
- `open-questions.md` — what we STILL don't know after research. often the most valuable output

## Execution Instructions

When this method is selected for a research question:

1. **Load context**: Read `wiki/index.md`. Pull relevant `wiki/concepts/`, `wiki/entities/`, `wiki/data-points.md` as verified background. You are NOT starting from zero.
2. **Create brief**: Write `projects/[topic]/brief.md` with question, scope, time horizon, output goal, `method: lens-research`, and relevant wiki background.
3. **Select framework**: Read [[research-frameworks]] to pick the right approach for this question type.
4. **Prepare evaluation**: Read [[source-evaluation]] so you know what counts as good evidence.
5. **Run lenses**: For EACH lens in order (technical → economic → historical → geopolitical → contrarian → first-principles):
   a. Read the lens file for its specific angle and questions
   b. Research the topic THROUGH that lens only
   c. Record findings, sources, and confidence level
   d. Note any contradictions with previous lenses
6. **Resolve contradictions**: Read [[contradiction-protocol]] — resolve or document disagreements between lenses.
7. **Synthesize**: Read [[synthesis-rules]] — combine everything without flattening.
8. **Produce outputs**: Write all 4 output files in `projects/[topic]/`.
9. **Log**: Append entry to `projects/task-log.md` with `Method = lens-research`.

**CRITICAL RULE**: Each lens must RETHINK the question, not just add more information to the same pile.
