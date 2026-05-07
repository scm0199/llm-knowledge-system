# Save Index

User-directed saved conversation assets.

This directory is for material the user explicitly asks the AI to save, but which is not yet a Raw source, a structured research project, or canonical Wiki knowledge.

## What belongs here

- Important discussion summaries
- Design decisions
- Workflow sketches
- Reusable prompts or checklists
- Interim conclusions
- Conversation-derived notes that may later become a project or wiki page

## What does not belong here

- External source snapshots → `raw/`
- Structured research deliverables → `projects/`
- Canonical long-term knowledge → `wiki/`
- Research methods or templates → `methods/`

## Naming

Use:

```text
YYYY-MM-DD-[slug].md
```

If the saved note updates a previous saved note, create a new versioned file:

```text
YYYY-MM-DD-[slug]-v2.md
```

## Promotion

Saved notes are reviewable conversation assets. They do not become canonical knowledge automatically.

Later actions may:

- turn a saved note into a `research [topic]` project
- use it as context for `query`
- absorb selected content into `wiki/` after review
- leave it as a lightweight saved note

## External Sync

Saved notes can be synced outward when the user explicitly asks.

Principles:

- `save/` is the canonical local record
- external destinations are one-way copies
- supported destinations may include Google Drive, Notion, another local folder, or other explicit locations
- v1 does not pull external edits back into `save/`
- v1 does not merge divergent copies
- sync failure does not invalidate the local saved note

Use:

```text
save-sync [destination] [topic or save-file]
```

Recommended sync record inside a saved note:

```markdown
## Sync Record

| Destination | External location | Synced at | Status | Notes |
|---|---|---|---|---|
| [destination] | [URL or path] | YYYY-MM-DD HH:mm | synced / failed | [details] |
```

## Saved Notes

<!-- Add saved note links here. -->
