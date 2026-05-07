# LLM Knowledge System — Master Specification

**Version**: v2.0 (2026-05-07) — Operation grouping + protocol-fit clean-ups. Operations regrouped into Input / Processing / Maintenance; session-start checklist fixed; `Conversion Record` slimmed to 3 required + 7 optional; new lint rule for `save/` misclassification; `notebooklm-analysis` external-dependency status surfaced.

This is the operating manual for the AI agent maintaining this knowledge system.
Read this file FIRST at the start of every session.

## Changelog

### v2.0 (2026-05-07) — Operation grouping + protocol-fit clean-ups
- **Operations regrouped into 3 phases**: Input (`stage` / `save` / `save-sync`) · Processing (`ingest` / `query` / `research`) · Maintenance (`lint` / `absorb`). Flat 8-operation listing was losing guidance value once the count crossed 6.
- **Session-start checklist fixed**: previously omitted `save-sync`; now lists all 8 operations using the new 3-phase grouping.
- **`stage` vs `save-sync` direction contrast added**: `stage` is external → local immutable Raw snapshot; `save-sync` is local → external one-way copy. Documented side-by-side to prevent confusion.
- **`Conversion Record` slimmed**: 10 flat fields → 3 required (`Conversion needed` / `Method` / `Status`) + 7 optional (filled only when meaningful). Reduces N/A noise in staged Raw files.
- **New lint rule for `save/`**: `lint` Part A now flags saved notes whose content looks more like Raw source material or wiki candidates — i.e. `save/` misclassification.
- **`notebooklm-analysis` external-dependency status surfaced**: methods table and selector now mark it as relying on an external cloud service; produces lower reproducibility than `lens-research` / `deep-search`. Fall-through default unchanged (used only when the user explicitly asks for NotebookLM).
- **No structural break**: 5-layer architecture, frontmatter schema, type taxonomy, Fact Promotion Rules, admission gate, and lineage rule are unchanged from v1.9.

### v1.9 (2026-05-06) — Optional MarkItDown stage conversion
- **Stage can accept external local file paths**: files outside this knowledge-base folder may be read during `stage`; the original files are not moved or copied into `raw/`
- **Optional MarkItDown conversion**: when staging convertible local files such as PDF, DOCX, PPTX, XLSX, HTML, CSV, JSON, XML, ZIP, EPub, image, or audio, check whether `markitdown` is available and use it when present
- **Non-blocking fallback**: absence or failure of MarkItDown must not block `stage`; AI should continue with the best available extraction path and clearly record fallback limitations
- **Single Raw output rule**: conversion output is embedded inside the one staged Raw Markdown file; do not create a separate intermediate converted Markdown file unless the user explicitly asks
- **New `Conversion Record`**: staged files produced from converted or fallback-extracted local files must record converter availability, method, status, and limitations

### v1.8 (2026-05-05) — Save sync extension
- **New auxiliary operation `save-sync`**: save conversation material locally first, then sync a copy to an external destination such as Google Drive, Notion, or another file location
- **Local-first rule**: `save/` remains the canonical saved note location; external destinations are one-way sync copies
- **No merge in v1**: external edits are not pulled back, reconciled, or merged

### v1.7 (2026-05-05) — User-directed save layer
- **New layer `save/`**: user-directed saved conversation assets that are not Raw sources, structured research projects, or canonical Wiki knowledge
- **New operation `save`**: when the user says "save this", create a lightweight Markdown note in `save/`
- **New distinction**: query candidates are system-default saved answers awaiting `lint`; `save/` is explicit user-selected material
- System architecture expanded: 4 layers → 5 layers (`raw/` + `save/` + `wiki/` + `methods/` + `projects/`)

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
5. **User-directed save** — When the user explicitly says to save a conversation result, preserve it in `save/` without forcing it into `raw/`, `projects/`, or `wiki/` prematurely.

## System Overview

This system has five layers:

1. **raw/** — Raw source documents. AI reads only during analysis; `stage` can create new immutable Raw Markdown snapshots when the user explicitly asks to capture external sources, including external local files that remain outside this folder.
2. **save/** — User-directed saved conversation assets. AI writes here only when the user explicitly asks to save a discussion, decision, workflow, prompt, or interim conclusion.
3. **wiki/** — Persistent knowledge base. AI creates and maintains all content.
4. **methods/** — Library of research/analysis tools. Each subfolder is one self-contained method. A top-level selector (`methods/index.md`) picks the right tool per task.
5. **projects/** — Per-task output workspace. AI writes project deliverables here. Shared task log at `projects/task-log.md`.

---

## Layer 1: Raw Sources (`raw/`)

External documents organized by content type. During analysis operations (`ingest`, `query`, `research`, `lint`, `absorb`), AI reads Raw files but never modifies them.

External storage tools (Notion, Google Drive, Feishu Docs, Yuque, Dropbox, GitHub, cloud folders, local filesystem paths outside this knowledge base, etc.) are acquisition channels, not Raw categories. If the user asks to bring external files into the system, use `stage` first: create a new local Markdown file under the existing `raw/` subfolder that matches the content type. The original external file is not moved or copied into `raw/`; the staged Raw Markdown snapshot is the local canonical capture. After creation, that staged file is immutable Raw material and later usage is logged outside the Raw file.

| Subfolder | Content |
|---|---|
| `meetings/` | Meeting transcripts (e.g. Plaud recordings). Named `YYYY-MM-DD-topic.md` |
| `articles/` | Clipped web articles (Obsidian Web Clipper, manual saves) |
| `reports/` | PDF reports, whitepapers, research papers |
| `manuscripts/` | Personal writings, reflections, self-directed thinking |
| `misc/` | Everything else: email threads, chat logs, specs |

Choose the subfolder by what the content is, not where it is stored. A Notion manuscript goes under `raw/manuscripts/`; a Google Drive meeting note goes under `raw/meetings/`; a Feishu report goes under `raw/reports/`.

### Stage Record for External Sources

`stage` is not an analysis operation. It is a user-authorized source-capture operation: the AI helps the user turn specified external source material into a new local Raw Markdown file, equivalent to the user manually copying, converting, organizing, and saving the material into `raw/`.

Stage-created Raw files must include a body-level record before the content. YAML is not required. Do not add a dynamic usage log to Raw files; after creation, later usage is recorded in `wiki/log.md`, the corresponding `wiki/sources/` page, project source appendices, or other dedicated logs.

### Optional Local File Conversion During Stage

When staging an external local file that is not already Markdown or plain text, prefer converting it into Markdown before writing the staged Raw file. This is especially useful for PDF reports, Word documents, PowerPoint decks, Excel workbooks, HTML exports, CSV/JSON/XML files, ZIP archives, EPubs, images, and audio files.

MarkItDown is an optional enhancement, not a hard dependency:

1. **Check availability lightly**: for convertible local files, check whether `markitdown` is available, for example with `markitdown --version`.
2. **If available, use it**: convert the source file to Markdown and embed the converted Markdown directly under `## Content` in the single staged Raw file.
3. **If unavailable, do not block**: continue `stage` using the best available fallback path, such as direct text extraction, built-in document tooling, metadata-only capture, user-provided excerpts, or AI-assisted structured notes.
4. **If conversion fails, do not silently degrade**: continue with fallback only if the fallback quality and limitations are explicitly recorded.
5. **Do not create intermediate Raw-adjacent files**: the staged Raw Markdown file is the only output unless the user explicitly asks to keep a separate conversion artifact.
6. **Be honest about completeness**: if fallback extraction is partial, metadata-only, OCR-limited, or summary-only, record that in `Conversion Record` and `Excluded / Missing Files`.

Scanning/OCR-heavy PDFs and image/audio extraction may require extra tools, model/API access, or privacy-sensitive processing. Do not enable OCR/vision/transcription workflows silently; record the available method and ask before using external services.

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

## Conversion Record

**Required (always fill):**

- Conversion needed: yes / no
- Conversion method: MarkItDown CLI / direct text extraction / built-in document tooling / AI-assisted extraction / metadata-only snapshot / user-provided copy / not needed / other
- Conversion status: succeeded / fallback / partial / failed / not needed

**Optional (fill only when meaningful — omit lines that would be N/A):**

- Converter preferred: [if a specific tool was attempted first, e.g. MarkItDown]
- Converter available: yes / no / unknown
- Converter version: [version string if relevant]
- Fallback used: yes / no
- Output completeness: partial content / metadata only / summary only / unknown
- Intermediate artifact kept: [path if user explicitly requested one]
- Notes: [warnings, OCR limits, unreadable pages, unsupported formats, dependency warnings]

If `Conversion needed: no`, fill only the three required lines with `not needed` / `not needed` / `not needed`.

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

## Layer 2: Saved Conversation Assets (`save/`)

The `save/` directory stores material the user explicitly asks to preserve from conversation.

Use `save/` when the user says things like:

- "save this"
- "把这个保存一下"
- "keep this as a note"
- "record this decision"
- "save this workflow/prompt/checklist"

`save/` is not a source-of-truth layer and not a research workspace. It is a lightweight user-curated holding area for conversation-derived material that may later become a project, a wiki page, or simply remain a saved note.

### What belongs in `save/`

- Important discussion summaries
- Design decisions
- Workflow sketches
- Reusable prompts or checklists
- Interim conclusions
- Conversation-derived notes that may later become a `research` project or wiki page

### What does not belong in `save/`

- External source snapshots → use `stage` into `raw/`
- Structured research deliverables → use `research` into `projects/`
- Canonical long-term knowledge → use `wiki/` through `query`, `ingest`, `lint`, or `absorb`
- Method rules and templates → use `methods/`

### Save file naming

Use a date-prefixed slug:

```
save/[YYYY-MM-DD]-[slug].md
```

If saving a revised version of an earlier note, create a new versioned file:

```
save/[YYYY-MM-DD]-[slug]-v2.md
```

Do not overwrite existing saved notes unless the user explicitly asks for a direct correction to a freshly created note.

### Recommended saved note format

```markdown
# [Saved Note Title]

## Save Record

- Save request: [user's request]
- Saved by: AI
- Created at: YYYY-MM-DD HH:mm
- Source: current conversation
- Intended use: reference / future research / workflow / decision record

## Content

[Saved discussion summary, decision, workflow, prompt, checklist, or interim conclusion.]

## Possible Next Actions

- query: [if useful]
- research: [if this should become a project]
- absorb: [if selected content should enter wiki after review]
```

### Save index

Update `save/index.md` whenever a new saved note is created.

### External sync for saved notes

Saved notes may be copied to external destinations when the user explicitly asks to sync them.

Supported request shapes:

- "save and sync to Google Drive"
- "保存并同步到 Notion"
- "sync this saved note to [destination]"
- "把 save/[file] 同步到 [地点]"

Rules:

1. **Local first** — always create or use the local `save/` file before syncing externally.
2. **External is a copy** — Google Drive, Notion, or other destinations are sync libraries, not the canonical record.
3. **One-way only** — v1 sync is local → external. Do not pull external edits back into `save/`.
4. **No merge** — do not reconcile divergent external copies. If the user wants a changed external version preserved, treat it as a new explicit save or stage action after discussion.
5. **Failure isolation** — if external sync fails, the local save still succeeds.
6. **Record sync status** — record destination, external link/path, synced time, and status in the saved note or `save/index.md`.

Recommended sync record:

```markdown
## Sync Record

| Destination | External location | Synced at | Status | Notes |
|---|---|---|---|---|
| Google Drive / Notion / Other | [URL or path] | YYYY-MM-DD HH:mm | synced / failed | [details] |
```

---

## Layer 3: Wiki (`wiki/`)

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

## Layer 4: Methods (`methods/`)

A library of research/analysis tools. Each subfolder is a self-contained method with its own `index.md`, methodology, and templates. A top-level selector (`methods/index.md`) chooses the right tool for a given task — the `research` command always consults the selector first.

Currently available methods:

| Method | Core mechanism | Best for | External dependency |
|---|---|---|---|
| `methods/lens-research/` | Single-threaded × 6 fixed analytical lenses (technical / economic / historical / geopolitical / contrarian / first-principles) | Interpretive depth through multi-perspective reasoning | None — fully local |
| `methods/deep-search/` | Parallel multi-agent investigation + consensus matrix + Socratic examination | Fact/current-state breadth with cross-validation | Web search only |
| `methods/notebooklm-analysis/` | NotebookLM notebook connection + source diff + five incremental artifacts | Cloud-assisted notebook-level synthesis and advanced NotebookLM outputs | **Requires NotebookLM (Google cloud service); reproducibility is lower than the other two methods** |

The `notebooklm-analysis` method violates the system's "filesystem + Markdown self-contained" preference, so the selector defaults to it **only when the user explicitly asks for NotebookLM** or NotebookLM-derived artifacts. If NotebookLM is unavailable, fall back to `lens-research` or `deep-search`.

To add a new method: create `methods/[name]/index.md`, register it in `methods/index.md`, done. The `research` command and this file do NOT need updating — the selector layer handles routing.

---

## Layer 5: Projects (`projects/`)

Each task (research / analysis / deep-search) gets its own directory under `projects/[topic-name]/`. The exact deliverable set is defined **by the chosen method**, not by this file.

- `lens-research` produces: `brief.md` + `executive-summary.md` + `deep-dive.md` (by lens) + `key-players.md` + `open-questions.md`
- `deep-search` produces: `brief.md` + `executive-summary.md` + `deep-dive.md` (by angle) + `consensus-matrix.md` + `socratic-review.md` + `source-appendix.md` + `open-questions.md`
- `notebooklm-analysis` produces: `brief.md` + `00-index.md` + `metadata.json` + `run-log.md` + NotebookLM-derived incremental artifacts defined by its templates

Common:
- `brief.md` — always has `method: [chosen-tool]` field, plus question/scope/wiki-background
- `open-questions.md` — always produced; seeds the next iteration

Shared log: **`projects/task-log.md`** — one row per project, with `Method` column so you can filter by tool.

---

## Operations

Operations are grouped into three phases. The grouping is for orientation only — every operation still triggers on its own command.

### Phase 1 — Input (bring material into the system)

- `stage` — capture external sources into immutable Raw Markdown snapshots
- `save` — preserve user-selected conversation material into `save/`
- `save-sync` — save locally, then sync a one-way copy to an external destination

### Phase 2 — Processing (turn material into knowledge)

- `ingest` — process a Raw document into `wiki/`
- `query` — answer a question from `wiki/` and file the answer as a candidate
- `research` — launch a method-driven project under `projects/`

### Phase 3 — Maintenance (keep the wiki healthy)

- `lint` — structural health check + candidate batch review
- `absorb` — promote project findings into `wiki/` under the admission gate

### Direction contrast: `stage` vs `save-sync`

These two commands look superficially similar (both touch external systems and both are "snapshot-like"), but they move in opposite directions and serve different roles:

| | `stage` | `save-sync` |
|---|---|---|
| Direction | external → local | local → external |
| Canonical record | the new local Raw file | the local `save/` file |
| Purpose | capture an outside source as immutable evidence | publish/back up a saved local note |
| Mutability of target | target Raw file is immutable after creation | external copy is a derivative; further edits not reconciled back |
| Triggers on | "stage this Notion page / PDF / Drive doc" | "save this and sync to Drive / Notion" |

If the user is bringing something *in*, use `stage`. If the user is sending something *out*, use `save-sync`.

---

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
2. **Handle external local file conversion if useful**: if the source is a local file path and its format benefits from conversion (PDF, DOCX, PPTX, XLSX, HTML, CSV/JSON/XML, ZIP, EPub, image, audio, etc.), lightly check whether MarkItDown is available. Use it when available; otherwise continue with the best fallback extraction path. MarkItDown absence must not block `stage`.
3. **Check for duplicates**: before creating any new file, search the target `raw/` subfolder for files referencing the same `Source location`. If a match exists, follow the **Deduplication Behavior** above (ask the user: replace / new versioned snapshot / skip).
4. **Choose the target `raw/` subfolder by content type, not by source tool**. A Notion manuscript → `raw/manuscripts/`; a Drive meeting note → `raw/meetings/`; a Feishu report or local PDF report → `raw/reports/`. Never create source-tool subfolders.
5. **Choose the filename** following the **File Naming Convention** above (`[YYYY-MM-DD]-[slug](-v[N]).md`).
6. **Create one Raw Markdown file** with `Stage Record`, `Source Record`, `Conversion Record` when applicable, included/excluded file list, and the captured/converted/fallback content. Do not create a separate converted Markdown file unless the user explicitly asks.
7. **Immutability**: the created file is Raw material from this point forward. Later AI operations read it but do not modify it. If the external source changes, create a new versioned snapshot per step 3 — never edit the old one.
8. **Report the created Raw path** to the user, including any issues from steps 1–3 (failed sub-pages, conversion fallback, partial captures, version uncertainty). Do not run `ingest` unless the user asks for it.

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

### `save [topic or instruction]`

Save user-selected conversation material into `save/`.

Use this operation when the user explicitly asks to preserve something from the conversation, but the material does not clearly belong in `raw/`, `projects/`, or canonical `wiki/` yet.

1. **Identify the save scope**: infer what the user wants saved from the immediate conversation. If the scope is ambiguous or could include sensitive material, ask a concise clarification before writing.
2. **Classify the saved note type**:
   - discussion summary
   - decision record
   - workflow sketch
   - prompt/checklist
   - interim conclusion
   - future research seed
3. **Choose filename** using `save/[YYYY-MM-DD]-[slug].md`.
4. **Check for duplicates**: if a closely related saved note already exists, ask whether to create a new note, create a versioned note, or skip.
5. **Write the saved note** using the recommended saved note format from Layer 2.
6. **Update `save/index.md`** with a link and one-line description.
7. **Report the saved path** to the user and suggest whether the note should later become `query`, `research`, or `absorb` input.

Rules:

- Do not treat `save/` content as `type: fact` evidence by itself.
- Do not use `save/` for external source capture; use `stage` instead.
- Do not use `save/` for structured research deliverables; use `research` instead.
- Do not automatically absorb saved notes into `wiki/`.

### `save-sync [destination] [topic or path]`

Save user-selected conversation material locally, then sync a copy to an external destination.

This is an auxiliary operation built on top of `save`. It can also sync an existing saved note.

Accepted user phrasings include:

- `save-sync Google Drive [topic]`
- `save-sync Notion [topic]`
- "保存并同步到 Google Drive"
- "把 save/[file] 同步到 Notion"

Execution:

1. **Determine mode**:
   - If the user is saving current conversation material, run `save` first.
   - If the user provides an existing `save/[file]`, read that file.
2. **Confirm destination**: Google Drive, Notion, local folder, or other explicit destination.
3. **Choose sync channel**: connector first, then API/CLI, then manual export if needed.
4. **Create or update external copy**.
5. **Record sync status** in the local saved note or `save/index.md`.
6. **Report both locations**: local save path and external URL/path.

Rules:

- `save/` remains canonical.
- External sync is one-way local → external.
- Do not merge external changes back into local saved notes.
- External sync failure does not invalidate the local save.
- Do not sync sensitive material unless the user explicitly requested that destination and the scope is clear.

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

Also scan `save/` for misclassification (v2.0):
- A saved note that reads as an external source snapshot (full document content, formal source metadata) should likely be `stage`d into `raw/` instead
- A saved note that reads as a finished Q&A with citations to wiki pages should likely be a `wiki/analysis/candidates/` entry instead
- A saved note that reads as a structured research deliverable should likely become a `projects/` workspace instead

Flag candidates for relocation; do not auto-move. The user adjudicates during Part B or in a follow-up.

**Part B — Candidate review (batch confirmation).** Walk through each file in `wiki/analysis/candidates/` with the user. For each candidate, ask:
- **Promote** → move to `wiki/analysis/`, remove `status: candidate`, link from `wiki/index.md`
- **Keep as candidate** → leave in place (still useful, not yet confirmed)
- **Discard** → delete the file

Report findings with suggested fixes. Apply fixes only with user approval.

### `research [topic]`

Launch a research project. The specific mechanism depends on the method chosen by the selector.

1. **Read the selector**: `methods/index.md` — decides which tool fits this topic (`lens-research`, `deep-search`, `notebooklm-analysis`, or another)
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

1. Read this file (`CLAUDE.md` / `AGENTS.md` — both are mirrors)
2. Read `wiki/index.md` to understand current knowledge state
3. Read `wiki/log.md` (last 10 entries) to understand recent activity
4. Ask the user what they want to do, presented by phase:
   - **Input**: `stage` · `save` · `save-sync`
   - **Processing**: `ingest` · `query` · `research`
   - **Maintenance**: `lint` · `absorb`
