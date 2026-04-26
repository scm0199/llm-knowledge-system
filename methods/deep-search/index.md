# Deep Search — Method Command Center

A parallel multi-agent research method. Takes ONE question, decomposes it into 5–8 angles, investigates them in parallel across diverse sources, cross-validates via consensus matrix, then stress-tests via Socratic examination.

This is **one tool** in `methods/`. See `methods/index.md` for when to pick this vs `lens-research`.

## When to use `deep-search`

Pick this method when the question is **factual or current-state** ("what's the latest on X?", "what are the numbers?", "who is doing X?") and where single-source trust is dangerous. Its core value is **cross-validation across parallel independent investigations** — you get not just an answer, but a confidence-graded answer with documented disagreements.

Pick `lens-research` instead if the question is interpretive/strategic and the value is in *re-framing* rather than *verifying facts*.

## Architecture

```
User Topic
    ↓
Phase 0: Topic analysis & research planning
    ↓
Phase 1: Parallel multi-source investigation (5–8 sub-agents)
    ↓
Phase 2: Cross-validation via consensus matrix
    ↓
Phase 3: Socratic examination (5 question categories)
    ↓
Phase 4: Report generation → write files to projects/[topic]/
```

## Project Outputs (in `projects/[topic]/`)

Deep-search produces a slightly different file set than the default 4. The full deliverable:

- `brief.md` — research question, scope, method = `deep-search`, background from wiki
- `executive-summary.md` — 3–5 paragraphs, consensus findings first, disputes highlighted
- `deep-dive.md` — full findings organized by research angle
- `consensus-matrix.md` — the claim × agent cross-validation table (required)
- `socratic-review.md` — the 5-category philosophical stress-test (required)
- `source-appendix.md` — every source, typed and quality-assessed (required)
- `open-questions.md` — what remains unknown after examination

Minimum templates live in `methods/deep-search/templates/`.

## Critical Rules

1. **No fabrication** — every factual claim must trace to a source URL or reference. If no evidence exists, write "No evidence found". Never invent.
2. **No single-source trust** — a claim backed by only one source is `UNVERIFIED`, never `CONSENSUS`.
3. **Parallel first** — sub-agents launch together, not sequentially. Identical queries across sub-agents is waste; each must use a distinct search strategy.
4. **Complete reports only** — all 4 phases must finish before writing files. No partial deliverables.
5. **Language matches input** — detect the user's language and write the report in the same language. This file is in English; the output adapts.
6. **Wiki-aware** — before Phase 0, read `wiki/index.md` and pull related `wiki/concepts/` / `wiki/entities/` / `wiki/data-points.md`. Known findings are context, not targets for re-discovery.
7. **v1.5 frontmatter compliance** — every file written to `projects/[topic]/` carries the 5-field frontmatter (`id, domain, type, source_lineage, last_updated`). `type` reflects evidence quality: `fact` for CONSENSUS-grade findings only; `interpretation` / `hypothesis` otherwise.

## Execution Instructions

### Phase 0 — Topic analysis & research planning

Before launching any sub-agents:

1. **Read wiki context**: `wiki/index.md` + any obviously related `concepts/`, `entities/`, `data-points.md` rows. Note what is already known.
2. **Parse the topic**: Extract core subject, scope constraints (time/geography/domain), depth signal (overview vs deep), topic type (technical/business/scientific/social/historical/mixed).
3. **Decompose into 5–8 angles**. Default angle templates:

   | # | Angle | Question template |
   |---|---|---|
   | 1 | Core definition | "What exactly is [topic]? Official definitions, standards." |
   | 2 | Current state | "What is the current state? Latest developments, trends, data." |
   | 3 | Historical context | "How did it evolve? Origin, key milestones, paradigm shifts." |
   | 4 | Competing perspectives | "Opposing views, criticisms, alternatives, debates." |
   | 5 | Practical applications | "Real-world examples, case studies, implementations." |
   | 6 | Technical deep-dive | "Architecture, mechanisms, specifications." |
   | 7 | Future outlook | "Predictions, emerging trends, open problems." |
   | 8 | Expert opinions | "Authoritative sources, interviews, papers." |

   For non-technical topics, swap angles 6/7/8 for: statistical/empirical evidence, policy/regulatory landscape, societal/cultural impact.

4. **Write `projects/[topic]/brief.md`** using `templates/research-plan.md` as the skeleton. Record: topic, type, scope, chosen angles, and wiki background pulled in step 1.

### Phase 1 — Parallel multi-source investigation

Launch one sub-agent per angle (5–8 total), in parallel. Each sub-agent gets:
- The full topic description
- Its assigned angle
- A distinct search strategy (web search / documentation / code / academic / forums — pick per angle)
- The output format below

**No two sub-agents should use the same search strategy or query set.** Diversity is the whole point.

Each sub-agent returns findings in this structure (see `templates/agent-assignment.md` for the full briefing):

```xml
<research_findings agent="[N]" angle="[name]">
  <finding id="1">
    <claim>[One-sentence factual claim]</claim>
    <detail>[2–3 sentences of support]</detail>
    <source>[Full URL or citation]</source>
    <source_type>official_docs | academic | news | blog | code | forum | government | other</source_type>
    <confidence>HIGH | MEDIUM | LOW</confidence>
    <evidence>[Direct quote or data point]</evidence>
  </finding>
  <!-- more findings -->
  <contradictions>[Within-angle conflicts, both sides + sources]</contradictions>
  <gaps>[What the agent could NOT find]</gaps>
</research_findings>
```

**Do not proceed to Phase 2 until ALL sub-agents have returned.** If one fails, relaunch it before moving on.

### Phase 2 — Cross-validation via consensus matrix

1. **Extract claims** from all sub-agent returns. Normalize near-duplicates into canonical claims.
2. **Build the matrix** (use `templates/consensus-matrix.md`):

   | # | Claim | Ag.1 | Ag.2 | … | Ag.N | Status | Confidence |
   |---|---|---|---|---|---|---|---|
   | 1 | [Claim] | ✅ | ✅ | — | ✅ | CONSENSUS | HIGH |

3. **Classify each claim**:

   | Status | Condition | Confidence floor |
   |---|---|---|
   | `CONSENSUS` | ≥3 agents confirm, 0 contradict | HIGH |
   | `STRONG` | 2 agents confirm, 0 contradict | MEDIUM |
   | `DISPUTED` | ≥1 agent contradicts | Requires Socratic review |
   | `UNVERIFIED` | 1 source only, no corroboration | LOW |
   | `CONTRADICTED` | More contradictors than confirmers | LOW — flag for deep review |

4. **Check source diversity** — if all confirmations trace to the same primary source, downgrade to `UNVERIFIED` regardless of count.
5. **Flag all `DISPUTED` and `CONTRADICTED` claims** for Phase 3.
6. **List blind spots** — angles where no agent found useful information.

### Phase 3 — Socratic examination

Apply five categories of philosophical questioning to every CONSENSUS-grade, DISPUTED, and key UNVERIFIED finding. The goal is **not** to disprove — it's to surface what the evidence quietly assumes. Use `templates/socratic-review.md`.

| Category | Questions |
|---|---|
| **Clarification** | What exactly do we mean by [term]? Scope — universal or conditional? Is there ambiguity across sources? |
| **Assumption** | What are we assuming when we accept this? Is the assumption justified? What changes if it's false? |
| **Evidence** | How strong is the evidence? Methodology? Bias (survivorship / selection / confirmation)? Recency? |
| **Perspective** | Who benefits from this being true? Who is harmed? Whose view is missing? |
| **Implication** | If true, what necessarily follows? Second-order effects? Any contradictions with other findings? |

For each examined finding, record: hidden assumptions + validity rating, evidence quality + concerns, missing perspectives, implications, and a **revised confidence** (may differ from Phase 2).

Finally, produce an `open_questions` list (prioritized) and a `blind_spots` list.

### Phase 4 — Report generation

Write all deliverables to `projects/[topic]/`. Each file gets the v1.5 frontmatter. Language matches user input.

Files to write:
1. `brief.md` — already created in Phase 0; now finalized with results
2. `executive-summary.md` — 3–5 paragraphs, CONSENSUS findings first, disputes highlighted, top 3 open questions noted
3. `deep-dive.md` — findings organized by angle, each with claim + detail + confidence + sources
4. `consensus-matrix.md` — the Phase 2 matrix, plus per-cluster themes and blind spots
5. `socratic-review.md` — the Phase 3 examination, confidence revisions table, top open questions, blind spots
6. `source-appendix.md` — all sources grouped by type (official / academic / news / code / expert) + a quality-assessment table (authority, recency, relevance)
7. `open-questions.md` — the prioritized open-questions list from Phase 3 (these are the seeds for next iteration)

### Post-generation

1. **Append to `projects/task-log.md`**: date, topic, `Method = deep-search`, key finding (one line), overall confidence, link to `projects/[topic]/`.
2. **Report back to user**: file paths + 2–3 sentence verbal summary + explicit mention of any DISPUTED findings needing human judgment + top 3 open questions.
3. **Suggest `absorb [topic]`** to promote CONSENSUS findings into the wiki. The absorb admission gate will block anything that fails lineage/consistency checks — that's the safety net, not a bug.

## Anti-Patterns

| Violation | Severity |
|---|---|
| Fabricating sources or claims | **CRITICAL** |
| Marking a single-source finding as `CONSENSUS` | **CRITICAL** |
| Running sub-agents sequentially instead of in parallel | HIGH |
| Skipping Socratic examination | HIGH |
| Writing files before Phase 4 finishes | HIGH |
| All sub-agents using identical search queries | HIGH |
| Ignoring contradictions between sources | HIGH |
| Writing report in the wrong language (not matching user input) | MEDIUM |
| Omitting `source-appendix.md` | MEDIUM |
| Findings without confidence levels | MEDIUM |

## Integration with the rest of the system

- **Before**: `wiki/index.md` + related pages are pre-read (Phase 0 step 1). You are not starting from zero.
- **During**: Output files in `projects/[topic]/` use v1.5 frontmatter. `type: fact` requires `CONSENSUS` status; otherwise `interpretation` / `hypothesis`.
- **After**: `absorb [topic]` promotes findings into the wiki with admission checks (lineage / consistency / structure / domain). The admission gate deliberately filters down `deep-search` results — only well-sourced consensus claims make it to `type: fact` pages.

## Limitations (declare these in `executive-summary.md`)

- Research is limited to publicly available information accessible via the sub-agents' tools
- Temporal boundary: information available as of the report date
- Language bias: primary research in user's language may miss other-language sources
- No primary research (interviews, experiments) — only desk research
