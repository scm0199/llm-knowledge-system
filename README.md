# LLM Knowledge System

> A single-user, file-system-based personal knowledge base maintained collaboratively with an LLM agent (e.g. Claude Code).
> 一套由 LLM 代理（如 Claude Code）协作维护的、纯文件系统的单用户个人知识库。
>
> **Protocol version: v2.0 (2026-05-07).** Full changelog at the top of [`CLAUDE.md`](CLAUDE.md). Highlights: 5-layer architecture (raw / save / wiki / methods / projects); 8 operations grouped into Input / Processing / Maintenance phases; optional MarkItDown stage conversion; `notebooklm-analysis` is opt-in only.

[English](#english) · [中文](#中文)

---

## English

### What is this?

This is **not** a tool you install. It is a **protocol + folder layout + set of instructions** that an LLM agent reads and follows to help you build, query, and maintain a personal knowledge base over time.

The entire knowledge base lives as plain Markdown files with YAML frontmatter. No database. No vector store. No web app. Just files you can open in Obsidian, VS Code, or any text editor.

### Core design principles

1. **Objective / subjective separation** — Every page is tagged with `type: fact | interpretation | hypothesis | opinion`. Reliability is visible at a glance.
2. **Markdown + frontmatter as the universal currency** — Every page carries a 5-field YAML header. The filesystem + wikilinks is the source of truth.
3. **Lineage over assertion** — Every claim must trace back to explicit upstream evidence (`wiki/sources/`, raw file paths, project deliverables, or source appendices as appropriate). Unsourced content cannot be promoted to `fact`.
4. **Default-save, batch-confirm** — Query answers are auto-saved as candidates. Confirmation happens in batch during `lint`, not mid-conversation.

### Five-layer architecture

| Layer | Path | Who writes | Purpose |
|---|---|---|---|
| 1. Raw | `raw/` | You; AI only via user-authorized `stage` | Drop in articles, meeting notes, reports, manuscripts. AI reads only during analysis; external cloud documents and external local files such as PDFs/Office documents can first be staged into immutable local Markdown snapshots under the matching Raw content category. |
| 2. Save | `save/` | AI, only when user explicitly asks to save | User-directed saved conversation assets: decisions, workflows, interim conclusions, reusable prompts. External sync copies may be created, but `save/` remains canonical. |
| 3. Wiki | `wiki/` | AI | Structured pages: concepts, entities, products, sources, analyses. |
| 4. Methods | `methods/` | Pre-built | Research methods: `lens-research` (6-perspective depth), `deep-search` (multi-agent breadth), `notebooklm-analysis` (NotebookLM-assisted synthesis). |
| 5. Projects | `projects/` | AI | Per-task workspace. Each research project gets its own folder. |

### How to use it

1. **Open this folder in Claude Code** (or any LLM agent that can read `CLAUDE.md`).
2. The agent reads `CLAUDE.md` first — it contains the full operating protocol.
3. Talk to the agent using these commands:

| Command | What happens |
|---|---|
| `stage <external source>` | Capture external cloud/file-store/local-file material into a new immutable local Raw Markdown file; MarkItDown is used for convertible files when available, with fallback when unavailable |
| `ingest <path>` | Process a raw document into the wiki |
| `query <question>` | Answer using existing wiki knowledge |
| `save <topic>` | Save user-selected conversation material into `save/` |
| `save-sync <destination> <topic/path>` | Save locally first, then sync a one-way copy to an external destination |
| `lint` | Health check + batch-review pending candidates |
| `research <topic>` | Launch a structured research project |
| `absorb <topic>` | Fold research findings back into the wiki |

Full command list in [`commands.md`](commands.md). Full protocol in [`CLAUDE.md`](CLAUDE.md).

### Repo state

This repository can be used as a **starter template**, but a local working copy may already contain real knowledge pages, raw sources, and research projects.

For any active instance, treat `CLAUDE.md` as the operating protocol, and treat `wiki/overview.md`, `wiki/index.md`, and `wiki/log.md` as the current state of the knowledge base.

### License

MIT — see [LICENSE](LICENSE).

---

## 中文

### 这是什么？

这**不是**一个安装工具。它是一套**协议 + 目录结构 + 操作指令**，让 LLM 代理（比如 Claude Code）读懂并按规则帮你长期维护一个个人知识库。

整个知识库以**纯 Markdown 文件 + YAML frontmatter** 形式存在。没有数据库，没有向量库，没有 Web 应用。所有内容用 Obsidian、VS Code 或任何文本编辑器都能直接打开。

### 核心设计原则

1. **客观/主观分离** —— 每个页面都标 `type: fact | interpretation | hypothesis | opinion`，可信度一眼可见。
2. **Markdown + frontmatter 是通用货币** —— 每页必须带 5 字段 YAML 头。文件系统 + wikilink 就是真相来源。
3. **来源可追溯优先于主张** —— 每条断言都要追溯到明确的上游证据（按场景可为 `wiki/sources/`、raw 文件路径、项目交付物或 source appendix）。无来源的内容不能升级为 `fact`。
4. **默认保存，批量确认** —— 问答结果自动落候选区，下一次 `lint` 时批量审核，不打断你思考流。

### 五层架构

| 层 | 路径 | 谁写 | 作用 |
|---|---|---|---|
| 1. 原料 | `raw/` | 你；AI 仅可通过用户授权的 `stage` 创建新文件 | 文章、会议记录、报告、手稿往里扔。AI 在分析时只读不改；外部云端资料和本地外部 PDF/Office 等文件可先按内容类型暂存为对应 Raw 分类下的不可变本地 Markdown 快照。 |
| 2. 保存 | `save/` | AI，仅在用户明确要求保存时写入 | 用户主动保存的对话资产：决策、工作流、阶段性结论、可复用 prompt。可创建外部同步副本，但 `save/` 仍是主记录。 |
| 3. 知识库 | `wiki/` | AI | 结构化页面：概念、实体、产品、来源、分析。 |
| 4. 方法库 | `methods/` | 内置 | 研究方法：`lens-research`（六视角深度）、`deep-search`（多智能体广度）、`notebooklm-analysis`（NotebookLM 辅助综合）。 |
| 5. 项目 | `projects/` | AI | 单任务工作区，每次研究一个独立文件夹。 |

### 使用方法

1. **用 Claude Code（或任何能读 `CLAUDE.md` 的 LLM 代理）打开这个文件夹**。
2. 代理首先读取 `CLAUDE.md`——里面是完整的操作协议。
3. 用以下命令跟代理对话：

| 命令 | 作用 |
|---|---|
| `stage <外部来源>` | 将外部云端/文件存储/本地外部文件封存为新的、不可变的本地 Raw Markdown；可转写文件优先使用 MarkItDown，不可用时 fallback |
| `ingest <路径>` | 摄取一份原始文档进知识库 |
| `query <问题>` | 用已有知识回答问题 |
| `save <主题>` | 将用户明确要求保存的对话内容写入 `save/` |
| `save-sync <地点> <主题/路径>` | 先保存到本地，再单向同步副本到外部位置 |
| `lint` | 健康检查 + 批量审核候选答案 |
| `research <主题>` | 启动一次结构化深度研究 |
| `absorb <主题>` | 把研究结论吸收回知识库 |

完整命令清单见 [`commands.md`](commands.md)。完整协议见 [`CLAUDE.md`](CLAUDE.md)。

### 仓库状态

本仓库可以作为**初始模板**使用，但某个本地工作副本也可能已经包含真实的知识页面、原始材料和研究项目。

对任何正在使用的实例，请以 `CLAUDE.md` 作为操作协议入口，并以 `wiki/overview.md`、`wiki/index.md`、`wiki/log.md` 判断当前知识库状态。

### 许可证

MIT —— 见 [LICENSE](LICENSE)。
