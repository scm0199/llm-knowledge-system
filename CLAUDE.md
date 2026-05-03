# LLM Knowledge System — Master Specification

**Version**: v1.6 (2026-05-03) — External-source bridging release. Adds `stage` operation, formalizes Fact Promotion Rules, codifies LLM-source skepticism, broadens lineage definition.

This is the operating manual for the AI agent maintaining this knowledge system.
Read this file FIRST at the start of every session.

## Changelog

### v1.6 (2026-05-03) — External-source bridging
- **New operation `stage`**: capture external storage (Notion / Google Drive / Feishu Docs / Yuque / Dropbox / GitHub / cloud folders / etc.) into local immutable Raw Markdown files
- **New conceptual rule**: *acquisition channels ≠ Raw categories* — Raw subfolders organize by content type, never by source tool
- **New section: Fact Promotion Rules** — codifies when content can be tagged `type: fact`, including the explicit rule that LLM-generated raw files do not constitute valid `fact` evidence unless independently verified by non-LLM primary or authoritative sources
- **Lineage rule expanded**: upstream evidence may be `wiki/sources/`, raw file paths, project deliverables, or source appendices (was: only `wiki/sources/`)
- **Ingest enhanced**: handles staged "link-index" Raw files with user-confirmed scope expansion (index only vs expand linked content)
- **Naming convention for versioned staged Raw files**: `[YYYY-MM-DD]-[slug]-v[N].md`
- **Connector preference for stage**: prefer dedicated MCP connectors when available; fallback to CLI / manual export / user-provided copy
- **Stage dedup behavior**: if a same-source staged file already exists, ask the user before proceeding (replace / new versioned snapshot / skip)
- Operations list expanded: 5 → 6 commands

### v1.5 (2026-04-19) — Initial protocol
- Borrowed from 识海 (Knowledge Network 2.0) architecture, single-user edition
- Four-layer system: `raw/` + `wiki/` + `methods/` + `projects/`
- 5-field minimum frontmatter (id / domain / type / source_lineage / last_updated)
- Type-tagging objective/subjective (fact / interpretation / hypothesis / opinion)
- `absorb` admission gate (lineage / consistency / structure / domain checks)
- `query` candidate crystallization + `lint` batch review

## Design Principles (borrowed from 识海 2.0)

1. **Objective/Subjective separation** — Every wiki page is tagged with a `type` field (`fact | interpretation | hypothesis | opinion`) so the reliability level is visible at a glance.
2. **Markdown + frontmatter as the universal currency** — All pages carry a minimum 5-field YAML frontmatter. No database, no vector store; the filesystem + wikilinks is the source of truth.
3. **Lineage over assertion** — Every claim must trace back to explicit upstream evidence (`wiki/sources/`, raw file paths, project deliverables, or source appendices as appropriate). Unsourced content cannot be promoted from candidate to canonical.
4. **Default-save, batch-confirm** — Query answers are saved as candidates automatically and confirmed/discarded in batches during `lint`, so the user is not interrupted mid-thought.

## System Overview

This system has four layers:

1. **raw/** — Raw source documents. AI reads only during analysis; `stage` can create new immutable Raw files when the user explicitly asks to capture external sources.
2. **wiki/** — Persistent knowledge base. AI creates and maintains all content.
3. **methods/** — Library of research/analysis tools. Each subfolder is one self-contained method. A top-level selector (`methods/index.md`) picks the right tool per task.
4. **projects/** — Per-task output workspace. AI writes project deliverables here. Shared task log at `projects/task-log.md`.

---

## Layer 1: Raw Sources (`raw/`)

External documents organized by content type. During analysis operations (`ingest`, `query`, `research`, `lint`, `absorb`), AI reads Raw files but never modifies them.

External storage tools (Notion, Google Drive, Feishu Docs, Yuque, Dropbox, GitHub, cloud folders, etc.) are acquisition channels, not Raw categories. If the user asks to bring external files into the system, use `stage` first: create a new local Markdown file under the existing `raw/` subfolder that matches the content type. After creation, that staged file is immutable Raw material and later usage is logged outside the Raw file.

| Subfolder | Content |
|---|---|
| `meetings/` | Meeting transcripts (e.g. Plaud recordings). Named `YYYY-MM-DD-topic.md` |
| `articles/` | Clipped web articles (Obsidian Web Clipper, manual saves) |
| `reports/` | PDF reports, whitepapers, research papers |
| `manuscripts/` | Personal writings, reflections, self-directed thinking |
| `misc/` | Everything else: email threads, chat logs, specs |

Choose the subfolder by what the content is, not where it is stored. A Notion manuscript goes under `raw/manuscripts/`; a Google Drive meeting note goes under `raw/meetings/`; a Feishu report goes under `raw/reports/`.

### Stage Record for External Sources

`stage` is not an analysis operation. It is a user-authorized source-capture operation: the AI helps the user turn specified external source material into a new local Raw Markdown file, equivalent to the user manually copying, organizing, and saving the material into `raw/`.

Stage-created Raw files must include a body-level record before the content. YAML is not required. Do not add a dynamic usage log to Raw files; after creation, later usage is recorded in `wiki/log.md`, the corresponding `wiki/sources/` page, project source appendices, or other dedicated logs.

### File Naming Convention for Staged Raw Files

Use a date-prefixed, versioned slug format:

```
raw/[content-type]/[YYYY-MM-DD]-[slug].md         # first snapshot of a source
raw/[content-type]/[YYYY-MM-DD]-[slug]-v2.md      # second snapshot (different date if any)
raw/[content-type]/[YYYY-MM-DD]-[slug]-v3.md      # third snapshot
```

- `[YYYY-MM-DD]` — date of the staging action (when AI captured the snapshot)
- `[slug]` — short, lowercase, hyphenated identifier of the source content (not the source tool)
- `-v[N]` — only added from the second snapshot onward; first snapshot omits the version suffix
- For documents that are inherently dated (meeting transcripts, daily notes), the date prefix may match the document's own date rather than the staging date — use whichever is more useful for retrieval, and note the choice in the Stage Record's `Created at` vs source date fields

If the original source has a long or non-Latin title, use a transliterated/translated short slug; preserve the original title inside the file's `# Title` heading.

### Deduplication Behavior

Before creating a new staged file, AI must check whether a file with the same source location already exists in the target `raw/` subfolder. If a match is found, AI must pause and ask the user one of three choices:

- **Replace** — only valid for trivial corrections (e.g. fixing a copy-paste error in a freshly staged file). Generally avoid; immutability is the default.
- **New versioned snapshot** — create a new file with `-v[N]` suffix; both old and new are preserved. This is the default for "the source has changed and I want a new snapshot".
- **Skip** — do not stage; existing file is sufficient.

Do not silently overwrite or silently create duplicates. The user must adjudicate.

Recommended format:

```markdown
# [Title]

## Stage Record

- Stage request: [user's request for this capture]
- Requested by: user
- Performed by: AI / user
- Created at: YYYY-MM-DD HH:mm
- Target raw path: raw/[category]/[filename].md
- Intended use: ingest / research / reference
- Read-only note: After creation, this file is treated as immutable raw material. Later usage is logged outside this file.

## Source Record

- Source tool: Notion / Google Drive / Feishu Docs / Yuque / Dropbox / GitHub / Other
- Source location: [URL / folder path / workspace path]
- Access method: connector / CLI / manual export / user-provided copy
- External version observed at: YYYY-MM-DD HH:mm
- External last edited: [if available]
- Source scope: single page / page with children / folder / selected files / database selection
- Snapshot method: full content copy / structured export / selected excerpts / summarized notes / file list with metadata
- Version uncertainty: none / unknown last edited time / partial access / dynamic database / other

## Included Files

| Title | External location | Last edited | Inclusion note |
|---|---|---|---|
| [file/page title] | [URL/path] | [if available] | [full / excerpt / summary / metadata only] |

## Excluded / Missing Files

- [External file/page not accessible, intentionally excluded, or missing]
- [Version/content uncertainty]

## Content

[Full exported content, selected excerpts, structured notes, meeting transcript, manuscript text, report content, or file directory with relevant details.]
```

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
  - `fact` — Directly attested by explicit upstream evidence. No extrapolation.
  - `interpretation` — Synthesis or inference combining multiple facts. Must cite them.
  - `hypothesis` — Unverified claim worth tracking. Must state what evidence would confirm/refute it.
  - `opinion` — Author perspective (typical for `manuscripts/` ingests). Not asserted as truth.
- `source_lineage` — Comma-separated wikilinks to the upstream evidence this content depends on. For normal wiki knowledge pages, this usually means `wiki/sources/` pages; for source summary pages, it may point to the original `raw/...` file path; for project deliverables, it may point to other project files, cited wiki pages, or source appendices. A page with empty `source_lineage` can only be `opinion` or `hypothesis`, never `fact`.
- `last_updated` — ISO date of the last substantive edit.

**Enforcement**: `lint` flags any page missing or malforming these fields.

### Fact Promotion Rules

Use `type: fact` only when a claim passes the evidence threshold for its context:

- External source claims may be `fact` only when they are directly attested by a source summary or another explicit lineage target. No extrapolation.
- Personal manuscripts produce `opinion` by default, or `hypothesis` when they make a testable prediction. Do not promote author perspective to `fact`.
- **Academic-citation clause**: When a personal manuscript *cites* an externally-established academic fact (e.g. Luhmann's communication triad, Rogers' diffusion of innovations, Wegner's transactive memory) and accurately restates it, the cited content may be tagged `type: fact` even though its only `source_lineage` is the manuscript. The author-perspective restriction applies to *the author's own judgments*, not to *verifiable academic claims the author transmits*. Such pages must include a `Verification status` note in the body indicating that primary-source verification (the cited work itself) is pending. When a primary source is later staged/ingested, update `source_lineage` to include it and remove the note.
- Research project claims may become `fact` only when they are CONSENSUS-grade, source-diverse, and pass the `absorb` admission checks. STRONG claims usually become `interpretation`; UNVERIFIED, DISPUTED, CONTRADICTED, or LOW-confidence claims stay in projects/open questions unless the user explicitly adjudicates them.
- LLM-generated raw files are not valid evidence for `fact` pages unless independently verified by non-LLM primary or authoritative sources.

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

### `stage [external source]`

Capture external source material into Raw.

`stage` is the only AI operation that creates Raw files, and only when the user explicitly asks to bring external material into the system. It does not update `wiki/`, `methods/`, or `projects/`.

1. **Choose the read channel** in this preference order:
   1. **Dedicated MCP connector** for the source tool, if one is connected (e.g. Notion MCP for Notion, Google Drive connector for Drive). API-backed, fastest, lowest friction.
   2. **Source-tool CLI** if available (e.g. `gh` for GitHub).
   3. **Browser automation** (Chrome MCP / computer-use) for sources that have no connector but render in a browser.
   4. **Manual export** by the user (export to Markdown, PDF, etc.) when none of the above works.
   5. **User-provided copy** (paste into the chat) as last resort.
   Pick the highest-tier option that actually works for the source. Don't fall through to a slower tier silently — if a connector errors, debug or report it rather than reaching for browser automation.
2. **Check for duplicates**: before creating any new file, search the target `raw/` subfolder for files referencing the same `Source location`. If a match exists, follow the **Deduplication Behavior** above (ask the user: replace / new versioned snapshot / skip).
3. **Choose the target `raw/` subfolder by content type, not by source tool**. A Notion manuscript → `raw/manuscripts/`; a Drive meeting note → `raw/meetings/`; a Feishu report → `raw/reports/`. Never create source-tool subfolders.
4. **Choose the filename** following the **File Naming Convention** above (`[YYYY-MM-DD]-[slug](-v[N]).md`).
5. **Create the file** with `Stage Record`, `Source Record`, included/excluded file list, and the captured content or snapshot.
6. **Immutability**: the created file is Raw material from this point forward. Later AI operations read it but do not modify it. If the external source changes, create a new versioned snapshot per step 2 — never edit the old one.
7. **Report the created Raw path** to the user, including any issues from steps 1–2 (failed sub-pages, partial captures, version uncertainty). Do not run `ingest` unless the user asks for it.

**Partial-capture handling**: if the read channel only retrieves part of the requested source (e.g. 80 of 100 Notion sub-pages succeed), still create the staged file with what was captured, list the failures explicitly under `Excluded / Missing Files`, and surface this in the report so the user can decide whether to retry the missing parts as a separate stage call.

### `ingest [path]`

Process a raw source document into the wiki.

1. Read the source document in `raw/` (including staged external-source Markdown files)
2. If the source is a staged external-source Markdown file that functions as a link index rather than a full content snapshot, identify all linked external URLs/files and ask the user to confirm the ingest scope before analysis: **index only** vs **expand linked content**. Do not fetch or analyze linked external content until the user confirms the scope.
3. Discuss key takeaways with the user
4. Create a source summary page in `wiki/sources/` (frontmatter `type: fact`, lineage points to the raw file path)
5. For each entity (person/org) found → create or update page in `wiki/entities/` (`type: fact`)
6. For each product found → create or update page in `wiki/products/` (`type: fact` for verified, `interpretation` for inferred positioning)
7. For each concept found → create or update page in `wiki/concepts/` (`type: fact` if defined by the source, `interpretation` if synthesized)
8. Add new terms to `wiki/glossary.md`
9. Add hard numbers to `wiki/data-points.md` (each row cites its source)
10. Update `wiki/index.md` with all new pages
11. Update `wiki/overview.md` if the big picture changed
12. Log everything in `wiki/log.md` with timestamp

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
4. Ask the user what they want to do: stage, ingest, query, lint, research, or absorb
