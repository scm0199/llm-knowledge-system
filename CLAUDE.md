# LLM Knowledge System — Master Specification

**Version**: v1.5 (2026-04-19) — MVP Lite of the 识海 (Knowledge Network 2.0) architecture, single-user edition.

This is the operating manual for the AI agent maintaining this knowledge system.
Read this file FIRST at the start of every session.

## Design Principles (borrowed from 识海 2.0)

1. **Objective/Subjective separation** — Every wiki page is tagged with a `type` field (`fact | interpretation | hypothesis | opinion`) so the reliability level is visible at a glance.
2. **Markdown + frontmatter as the universal currency** — All pages carry a minimum 5-field YAML frontmatter. No database, no vector store; the filesystem + wikilinks is the source of truth.
3. **Lineage over assertion** — Every claim must trace back to a `sources/` page. Unsourced content cannot be promoted from candidate to canonical.
4. **Default-save, batch-confirm** — Query answers are saved as candidates automatically and confirmed/discarded in batches during `lint`, so the user is not interrupted mid-thought.

## System Overview

This system has four layers:

1. **raw/** — Raw source documents. AI reads, NEVER writes.
2. **wiki/** — Persistent knowledge base. AI creates and maintains all content.
3. **methods/** — Library of research/analysis tools. Each subfolder is one self-contained method. A top-level selector (`methods/index.md`) picks the right tool per task.
4. **projects/** — Per-task output workspace. AI writes project deliverables here. Shared task log at `projects/task-log.md`.

---

## Layer 1: Raw Sources (`raw/`)

External documents organized by type. AI reads these during `ingest` but never modifies them.

| Subfolder | Content |
|---|---|
| `meetings/` | Meeting transcripts (e.g. Plaud recordings). Named `YYYY-MM-DD-topic.md` |
| `articles/` | Clipped web articles (Obsidian Web Clipper, manual saves) |
| `reports/` | PDF reports, whitepapers, research papers |
| `manuscripts/` | Personal writings, reflections, self-directed thinking |
| `misc/` | Everything else: email threads, chat logs, specs |

### Ingest behavior by source type

- **meetings**: Extract decisions, action items, participants → update `wiki/entities/`, create source summary
- **articles**: Extract key claims, concepts, data → update `wiki/concepts/`, `wiki/data-points.md`
- **reports**: Full structured extraction → may create multiple concept/entity/product pages
- **manuscripts**: Extract personal viewpoints, hypotheses, evolving thinking → treat as opinion, not fact. Link to related concepts but mark as "author perspective"
- **misc**: General extraction, same as articles

---

## Layer 2: Wiki (`wiki/`)

The persistent, structured knowledge base. AI owns all content here.
All pages use `[[wikilinks]]` for cross-referencing.

### Minimum Frontmatter (required on every wiki page)

Every page under `wiki/` (except the core files `index.md`, `log.md`, `overview.md`, which are operational) must begin with a YAML frontmatter block:

```yaml
---
id: [filename-without-extension]
domain: [field/area — e.g. ai-safety, economics, product-design]
type: fact | interpretation | hypothesis | opinion
source_lineage: [[source-page-1]], [[source-page-2]]
last_updated: YYYY-MM-DD
---
```

**Field meaning:**

- `id` — Must match the filename (without `.md`). Stable identifier for wikilinks.
- `domain` — One or two lowercase tags for the field/area. Helps `lint` detect cross-domain contamination.
- `type` — The **objective/subjective level** of the page:
  - `fact` — Directly attested by at least one source in `sources/`. No extrapolation.
  - `interpretation` — Synthesis or inference combining multiple facts. Must cite them.
  - `hypothesis` — Unverified claim worth tracking. Must state what evidence would confirm/refute it.
  - `opinion` — Author perspective (typical for `manuscripts/` ingests). Not asserted as truth.
- `source_lineage` — Comma-separated wikilinks to every `sources/` page this content depends on. A page with empty `source_lineage` can only be `opinion` or `hypothesis`, never `fact`.
- `last_updated` — ISO date of the last substantive edit.

**Enforcement**: `lint` flags any page missing or malforming these fields.

### Core Files

| File | Purpose | Updated by |
|---|---|---|
| `index.md` | Master directory of all wiki pages | Every ingest/absorb |
| `overview.md` | Bird's-eye synthesis of all knowledge | When the big picture shifts |
| `glossary.md` | Terms, abbreviations, definitions, deprecated names | Every ingest/absorb |
| `data-points.md` | Hard numbers, statistics, metrics with attribution | Every ingest/absorb/research |
| `log.md` | Timestamped operation log | Every operation |

### Page Directories

| Directory | Page type | One page per... |
|---|---|---|
| `sources/` | Source summary | Each raw document ingested |
| `concepts/` | Concept/idea | Each important concept across documents |
| `entities/` | Person or organization | Each person/org mentioned significantly |
| `products/` | Product (with sub-pages) | Each product/tool/platform |
| `comparison/` | Comparison table | Each meaningful A-vs-B analysis |
| `analysis/` | Saved query answer (canonical) | Each Q&A that has passed lint confirmation |
| `analysis/candidates/` | Candidate crystallizations | Every query answer — auto-saved, confirmed/discarded in next `lint` |

### Product Page Structure

Products get richer treatment. Each product is a subfolder:

```
wiki/products/[product-name]/
  ├── overview.md        # Positioning, core value, target users
  ├── architecture.md    # Technical architecture, system design
  ├── features.md        # Feature details, capabilities
  └── changelog.md       # Version history, evolution
```

Only create sub-pages when there is enough content. A single `overview.md` is fine for lightly-mentioned products.

### Entity Page Format

```markdown
---
id: [entity-slug]
domain: [field]
type: fact
source_lineage: [[source-page-1]]
last_updated: YYYY-MM-DD
---

# [Entity Name]

**Category**: Person | Organization | Team
**Role**: [what they do]
**First mentioned**: [[source-page]]

## Key Facts
- ...

## Connections
- Related to [[concept]] because...
- Works at/with [[entity]]
- Involved in [[product]]

## Timeline
- YYYY-MM-DD: [event] (source: [[source-page]])
```

### Concept Page Format

```markdown
---
id: [concept-slug]
domain: [field]
type: fact | interpretation
source_lineage: [[source-page-1]], [[source-page-2]]
last_updated: YYYY-MM-DD
---

# [Concept Name]

**Definition**: [one-line definition]
**First mentioned**: [[source-page]]

## Description
[2-3 paragraphs explaining the concept]

## Related Concepts
- [[related-concept-1]] — [relationship]
- [[related-concept-2]] — [relationship]

## Sources
- [[source-page-1]] — [what it says about this concept]
- [[source-page-2]] — [what it says about this concept]
```

### Analysis Candidate Page Format

```markdown
---
id: [slug-of-the-question]
domain: [field]
type: interpretation | hypothesis | opinion
source_lineage: [[wiki-pages-cited-in-answer]]
last_updated: YYYY-MM-DD
status: candidate
---

# Q: [original question]

## Answer
[the composed answer, with wikilinks]

## Cited pages
- [[page-1]]
- [[page-2]]

## Review notes
<!-- Reviewer (during lint) adds: keep / promote / discard + why -->
```

---

## Layer 3: Methods (`methods/`)

A library of research/analysis tools. Each subfolder is a self-contained method with its own `index.md`, methodology, and templates. A top-level selector (`methods/index.md`) chooses the right tool for a given task — the `research` command always consults the selector first.

Currently available methods:

| Method | Core mechanism | Best for |
|---|---|---|
| `methods/lens-research/` | Single-threaded × 6 fixed analytical lenses (technical / economic / historical / geopolitical / contrarian / first-principles) | Interpretive depth through multi-perspective reasoning |
| `methods/deep-search/` | Parallel multi-agent investigation + consensus matrix + Socratic examination | Fact/current-state breadth with cross-validation |

To add a new method: create `methods/[name]/index.md`, register it in `methods/index.md`, done. The `research` command and this file do NOT need updating — the selector layer handles routing.

---

## Layer 4: Projects (`projects/`)

Each task (research / analysis / deep-search) gets its own directory under `projects/[topic-name]/`. The exact deliverable set is defined **by the chosen method**, not by this file.

- `lens-research` produces: `brief.md` + `executive-summary.md` + `deep-dive.md` (by lens) + `key-players.md` + `open-questions.md`
- `deep-search` produces: `brief.md` + `executive-summary.md` + `deep-dive.md` (by angle) + `consensus-matrix.md` + `socratic-review.md` + `source-appendix.md` + `open-questions.md`

Common:
- `brief.md` — always has `method: [chosen-tool]` field, plus question/scope/wiki-background
- `open-questions.md` — always produced; seeds the next iteration

Shared log: **`projects/task-log.md`** — one row per project, with `Method` column so you can filter by tool.

---

## Operations

### `ingest [path]`

Process a raw source document into the wiki.

1. Read the source document in `raw/`
2. Discuss key takeaways with the user
3. Create a source summary page in `wiki/sources/` (frontmatter `type: fact`, lineage points to the raw file path)
4. For each entity (person/org) found → create or update page in `wiki/entities/` (`type: fact`)
5. For each product found → create or update page in `wiki/products/` (`type: fact` for verified, `interpretation` for inferred positioning)
6. For each concept found → create or update page in `wiki/concepts/` (`type: fact` if defined by the source, `interpretation` if synthesized)
7. Add new terms to `wiki/glossary.md`
8. Add hard numbers to `wiki/data-points.md` (each row cites its source)
9. Update `wiki/index.md` with all new pages
10. Update `wiki/overview.md` if the big picture changed
11. Log everything in `wiki/log.md` with timestamp

**Every new page must carry the minimum frontmatter (id, domain, type, source_lineage, last_updated).** If a claim has no source, it cannot be `type: fact` — use `hypothesis` or `opinion` instead.

**For manuscripts specifically**: extracted viewpoints get `type: opinion` (or `hypothesis` if they make a testable prediction). Link to related concepts but do not assert as fact.

### `query [question]`

Answer a question using the wiki.

1. Read `wiki/index.md` to locate relevant pages
2. Read relevant wiki pages (NOT raw files)
3. Compose answer with citations to wiki pages. Respect the `type` of each cited page (a `hypothesis`-typed source does not support a `fact`-level claim).
4. **Default-save**: Write the answer to `wiki/analysis/candidates/` with frontmatter `type: interpretation` (or `hypothesis`/`opinion` as appropriate) and `status: candidate`. Do NOT interrupt the user to ask.
5. Reply to the user with the answer and mention that the candidate has been filed for batch review during the next `lint`.

The user confirms/discards candidates in batch during `lint` — this avoids fragmenting the thinking flow.

### `lint`

Health check the wiki + review the candidate queue.

**Part A — Structural health check.** Scan all wiki pages for:
- Missing/malformed frontmatter (all 5 required fields present? `type` is one of the 4 valid values?)
- `type: fact` pages with empty `source_lineage` (violation — must be downgraded or sourced)
- Contradictions between pages (cross-check `fact`-typed claims)
- Outdated statements superseded by newer sources
- Orphan pages (no links pointing to them)
- Broken `[[wikilinks]]` (mentioned but missing pages)
- Inconsistent terminology vs `wiki/glossary.md`

**Part B — Candidate review (batch confirmation).** Walk through each file in `wiki/analysis/candidates/` with the user. For each candidate, ask:
- **Promote** → move to `wiki/analysis/`, remove `status: candidate`, link from `wiki/index.md`
- **Keep as candidate** → leave in place (still useful, not yet confirmed)
- **Discard** → delete the file

Report findings with suggested fixes. Apply fixes only with user approval.

### `research [topic]`

Launch a research project. The specific mechanism depends on the method chosen by the selector.

1. **Read the selector**: `methods/index.md` — decides which tool fits this topic (`lens-research`, `deep-search`, or another)
2. **Load wiki context**: Read `wiki/index.md` and pull relevant background pages. Never start from zero.
3. **Hand off to method**: Follow the execution instructions in `methods/[chosen-method]/index.md`. That file defines the phases, templates, and deliverable set for this task.
4. **Record the method**: Every `projects/[topic]/brief.md` carries a `method: [name]` field so future readers know how the project was conducted.
5. **Log**: Append one row to `projects/task-log.md` with date, topic, method, key finding, confidence, and link.

### `absorb [topic]`

Absorb research findings back into the wiki.

**Step 0 — Admission checks (lightweight promotion gate).** Before writing anything to `wiki/`, run these four checks on each candidate extraction:

1. **Lineage check** — Does every claim trace to at least one item in `sources/` or `projects/[topic]/`? If no source exists, the target page must be `type: hypothesis` or `opinion`, never `fact`.
2. **Consistency check** — Does the claim contradict an existing `fact`-typed wiki page? If yes → do NOT overwrite; file the contradiction under `wiki/overview.md#open-contradictions` and ask the user to adjudicate.
3. **Structure check** — Does the target page (new or updated) have valid frontmatter (5 required fields, valid `type`)?
4. **Domain check** — Is the `domain` tag reasonable for this claim? (Flag if mismatched with existing page.)

Only extractions that pass all four checks proceed below.

**Main absorb steps:**

1. Read all files in `projects/[topic]/`
2. Extract new concepts → create/update `wiki/concepts/` pages (apply admission checks)
3. Extract new entities → create/update `wiki/entities/` pages
4. Extract new products → create/update `wiki/products/` pages
5. Extract data points → append to `wiki/data-points.md`
6. Extract comparisons → create/update `wiki/comparison/` pages
7. Update `wiki/glossary.md` with new terms
8. Update `wiki/index.md` and `wiki/overview.md`
9. Log in `wiki/log.md` (include which items were deferred by the admission gate)

---

## Session Start Checklist

At the beginning of every session:

1. Read this file (`CLAUDE.md`)
2. Read `wiki/index.md` to understand current knowledge state
3. Read `wiki/log.md` (last 10 entries) to understand recent activity
4. Ask the user what they want to do: ingest, query, lint, research, or absorb
