# Methods — Research Tool Selector

This directory holds multiple research/analysis tools. Each subfolder is a self-contained method with its own `index.md`, methodology, and templates.

The `research [topic]` command always reads **this file first** to pick a tool, then hands off to that tool's own `index.md`.

## Available Methods

| Method | Core mechanism | Best for | Web-heavy? |
|---|---|---|---|
| [[lens-research/index]] | Single-threaded × 6 fixed analytical lenses | Interpretive depth through multi-perspective reasoning | Optional |
| [[deep-search/index]] | Parallel multi-agent investigation + consensus matrix + Socratic examination | Fact/current-state breadth with cross-validation | Required |

## How to Pick

Answer these three questions about the user's request:

**1. What kind of question is it?**
- "How should we think about X?" / "What does X really mean?" / "Is X a good idea?" → **lens-research** (interpretive)
- "What is the current state of X?" / "What are the numbers on X?" / "Who is doing X and how?" → **deep-search** (factual/current-state)

**2. How much does cross-validation matter?**
- Low — the topic has stable, well-understood facts, and the value is in reframing → **lens-research**
- High — you need to triangulate across live sources because single sources are untrustworthy or outdated → **deep-search**

**3. What does the user want out the other end?**
- Structured multi-perspective synthesis with a takeaway → **lens-research**
- Evidence-graded report with confidence levels and source appendix → **deep-search**

If the answers split 2-1, go with the majority. If you're still torn, default to **lens-research** for interpretive/strategic questions and **deep-search** for technical/empirical questions.

## When Neither Fits

If the question is genuinely small (one paragraph answer will do), skip `research` entirely — use `query` instead. The `research` command exists for questions that warrant a multi-file project in `projects/`.

## Method Selection — Record in `brief.md`

Whichever method you pick, record the choice and the reason in `projects/[topic]/brief.md` under a `Method` field. Logged to `projects/task-log.md` afterward.

## Adding a New Method

When a new method is added:
1. Create `methods/[method-name]/` with its own `index.md`
2. Add a row to the **Available Methods** table above
3. Update **How to Pick** questions if the new method covers a question shape not already handled
4. The `research` command and `CLAUDE.md` do **not** need updating — the selector layer handles the routing.
