# NotebookLM Analysis — Method Command Center

NotebookLM Analysis is a cloud-assisted advanced analysis method. It uses NotebookLM as a high-level synthesis and visualization engine, while keeping the local Obsidian knowledge system as the source of truth.

This method is designed for cases where the user has already collected related materials locally, but wants NotebookLM to produce notebook-level synthesis, cross-document understanding, or advanced artifacts that are difficult to reproduce through local file analysis alone.

This is **one tool** in `methods/`. See `methods/index.md` for when to pick this vs `lens-research` or `deep-search`.

## When to use `notebooklm-analysis`

Use this method when the task requires **NotebookLM-specific strengths**:

- The user wants to analyze a set of documents already organized in NotebookLM
- The user wants notebook-level synthesis across many uploaded PDFs or articles
- The user wants NotebookLM-generated artifacts such as study guides, briefing docs, audio overviews, mind maps, or other advanced outputs
- The user wants to compare NotebookLM's synthesis with local Obsidian / Codex analysis
- The user wants to treat Google Drive as an external library for NotebookLM-derived outputs

Do **not** use this method when:

- The materials only need local parsing, extraction, or summarization
- The source files have not been uploaded to NotebookLM and the user does not want to do so
- The task requires strict source-level factual verification; use `deep-search` instead
- The task is primarily interpretive and does not need NotebookLM artifacts; use `lens-research` instead

## Role in the Knowledge System

NotebookLM is not the system of record. It is an external analysis tool.

The intended hierarchy is:

```text
Obsidian raw / wiki
  = source of truth and long-term knowledge base

Codex / Claude Code
  = local analysis agent and system maintainer

NotebookLM
  = cloud advanced synthesis and artifact generator

Google Drive
  = external library for NotebookLM-derived outputs
```

NotebookLM outputs should be treated as **analysis artifacts**, not automatically promoted facts. Any factual claim produced by NotebookLM must still pass the system's lineage and fact-promotion rules before entering `wiki/` as `type: fact`.

## Core Capabilities

### 1. Connect to NotebookLM

The agent may use an available NotebookLM interface, such as:

- `notebooklm-py` CLI
- NotebookLM MCP, if available
- Browser-assisted manual workflow, if no programmatic interface works

Before running analysis, verify:

- NotebookLM authentication is valid
- The target notebook can be selected or created
- The notebook source list can be read
- The user understands that NotebookLM is an external cloud service

### 2. Use an Existing Notebook or Create a New Notebook

The method supports two notebook modes:

| Mode | Use when | Behavior |
|---|---|---|
| Existing notebook | The user already maintains a NotebookLM notebook for the topic | Select the notebook, inspect its source list, compare against previous state |
| New notebook | The user wants a fresh analysis workspace | Create or ask the user to create a notebook, then add sources manually or through available tooling |

If source upload cannot be automated reliably, the user may manually upload PDFs or source files to NotebookLM. The method should then continue from source-list inspection and analysis generation.

### 3. Detect Source Changes

For each NotebookLM notebook, maintain a local state record in the project folder.

The state should include:

- Notebook ID
- Notebook title
- Last checked time
- Source count
- Source snapshot
- Newly detected sources
- Generated output files
- Google Drive output links, if any

Source comparison is based on stable source identifiers when available. If source identifiers are unavailable, use a conservative combination of title, type, created time, and file metadata.

## Output Logic

This method has two execution paths:

1. No newly detected sources
2. One or more newly detected sources

### No New Sources

When no new sources are detected, the method should not generate new analysis artifacts.

It should update only operational files:

- `00-index.md`
- `metadata.json`
- `run-log.md`

The index should clearly state:

```text
No new sources detected.
No new analysis artifacts generated.
```

This avoids producing artificial updates and keeps NotebookLM-generated material meaningful.

### New Sources Detected

When new sources are detected, generate five classes of outputs using the templates in:

```text
methods/notebooklm-analysis/templates/
```

Template index:

- [[templates/01-source-summary]] — one file per newly detected source
- [[templates/02-incremental-insights]] — one file per update batch
- [[templates/03-trend-impact]] — one file per update batch
- [[templates/04-question-backlog]] — one file per update batch
- [[templates/05-concept-map-delta]] — one file per update batch

Naming rules and detailed document structures live in [[templates/00-index]].

## Project Outputs

NotebookLM Analysis writes outputs to:

```text
projects/[topic]/
```

Recommended file set:

- `brief.md` — topic, target notebook, method = `notebooklm-analysis`, scope, user intent
- `00-index.md` — current run index and generated file list
- `metadata.json` — notebook source snapshot and run state
- `run-log.md` — chronological operation log
- `source-summary` files — one per new source
- `incremental-insights` file — one per update batch
- `trend-impact` file — one per update batch
- `question-backlog` file — one per update batch
- `concept-map-delta` file — one per update batch
- `drive-links.md` — optional list of Google Drive files created or updated

If the workflow is being used as a recurring notebook monitor, operational state may also live under a dedicated state folder, but the human-readable outputs should remain in `projects/[topic]/`.

## Google Drive Integration

Google Drive is treated as an external library, not the primary knowledge base.

Use Google Drive for:

- NotebookLM-generated artifacts
- Exported briefing documents
- Audio overviews
- Mind maps or visual outputs
- Shareable reports
- Cloud backup of generated Markdown, if requested

Do not treat Google Drive files as canonical wiki knowledge until they are staged, ingested, or absorbed through the normal knowledge-system rules.

## Execution Instructions

### Phase 0 — Method Setup

1. Read `wiki/index.md`, `wiki/overview.md`, and any related pages.
2. Create `projects/[topic]/brief.md`.
3. Record:
   - Method: `notebooklm-analysis`
   - Target NotebookLM notebook
   - Whether the notebook is existing or newly created
   - Whether Google Drive sync is enabled
   - What source set the user expects to analyze

### Phase 1 — NotebookLM Connection

1. Check NotebookLM authentication.
2. List available notebooks.
3. Select the matching notebook or create a new one.
4. If source upload is manual, pause and ask the user to confirm when upload is complete.
5. Read the notebook source list.

### Phase 2 — Source Diff

1. Load previous source snapshot from `metadata.json`, if any.
2. Compare current NotebookLM sources against the previous snapshot.
3. Classify the run:
   - baseline run
   - no-new-source run
   - incremental update run
4. For a baseline run, record the source snapshot but do not treat all existing sources as new.

### Phase 3 — Artifact Generation

If no new sources:

1. Update `00-index.md`.
2. Update `metadata.json`.
3. Append to `run-log.md`.
4. Skip NotebookLM generation.

If new sources exist:

1. Generate `01-source-summary` for each new source.
2. Generate `02-incremental-insights` for the update batch.
3. Generate `03-trend-impact` for the update batch.
4. Generate `04-question-backlog` for the update batch.
5. Generate `05-concept-map-delta` for the update batch.
6. Update `00-index.md`, `metadata.json`, and `run-log.md`.

### Phase 4 — External Library Sync

If Google Drive sync is enabled:

1. Ensure the target Drive folder exists.
2. Upload or update generated files.
3. Save Drive links in `drive-links.md` or `metadata.json`.
4. Record sync result in `run-log.md`.

### Phase 5 — Report Back

Return to the user:

- Notebook analyzed
- Number of sources checked
- Number of new sources detected
- Files generated
- Drive links, if any
- Failures or uncertainties
- Recommended next step: review outputs, then optionally `absorb [topic]`

## Absorb Guidance

NotebookLM outputs are project artifacts. They should not be automatically written into `wiki/`.

When the user runs:

```text
absorb [topic]
```

the agent should:

1. Read the NotebookLM Analysis project outputs.
2. Identify candidate facts, interpretations, hypotheses, questions, concepts, entities, products, and comparisons.
3. Apply the normal fact-promotion rules from `CLAUDE.md`.
4. Treat NotebookLM-only claims as `interpretation` or `hypothesis` unless independently verified.
5. Promote only suitable material into `wiki/`.
6. Update `wiki/index.md`, `wiki/log.md`, and related pages.

## Critical Rules

1. **NotebookLM is not source of truth** — local Raw/Wiki lineage remains authoritative.
2. **No automatic fact promotion** — NotebookLM output alone does not justify `type: fact`.
3. **No fake source diffs** — baseline runs must not treat all existing sources as newly added.
4. **No noisy no-op outputs** — if there are no new sources, do not generate artificial analysis artifacts.
5. **Preserve manual boundaries** — if upload to NotebookLM must be manual, pause and ask the user to confirm completion.
6. **Keep outputs reviewable** — all generated files should be Markdown, stable-named, and stored in the project folder.
7. **Record external links** — any Google Drive output must be traceable from project metadata or logs.

## Limitations

- NotebookLM is an external cloud service; authentication and interface behavior may change.
- Programmatic NotebookLM access may rely on unofficial tools and should be treated as brittle.
- NotebookLM may synthesize across sources without exposing enough citation structure for fact promotion.
- The user may need to manually upload sources or export advanced artifacts.
- Google Drive sync is useful for external-library storage but does not replace local Obsidian as the knowledge base.

## Anti-Patterns

| Violation | Severity |
|---|---|
| Treating NotebookLM output as canonical fact without verification | CRITICAL |
| Treating baseline sources as new incremental updates | HIGH |
| Generating five artifacts when no new source exists | HIGH |
| Uploading to Google Drive without recording links or file IDs | MEDIUM |
| Hiding NotebookLM failures or authentication uncertainty | MEDIUM |
| Writing NotebookLM artifacts directly into `wiki/` without `absorb` review | HIGH |
