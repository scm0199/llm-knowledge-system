# 用户命令参考

> 协议版本:**v1.6**(2026-05-03)。完整变更见 `CLAUDE.md` 顶部 Changelog。

## 核心操作命令

| 命令 | 说明 | 示例 | AI 行为 |
|---|---|---|---|
| `stage [external source]` | 将外部来源封存为本地 Raw Markdown | `stage Notion 中某手稿到 raw/manuscripts/` | 选 connector(优先 MCP → CLI → 浏览器 → 人工)→ 检查 dedup → 按内容类型选目录 → 用 `[YYYY-MM-DD]-[slug](-v[N]).md` 命名 → 写入 Stage Record / Source Record / Content → 不更新 wiki;生成后不可变 |
| `ingest [path]` | 摄取一份原始文档到 wiki | `ingest raw/articles/llm-wiki.md` | 读取文档；若 staged Raw 是链接索引，先确认只摄取索引还是展开链接 → 与你讨论要点 → 创建/更新 wiki 多个页面 |
| `ingest raw/[folder]/` | 批量摄取某个子目录下所有文档 | `ingest raw/meetings/` | 逐一处理目录下每份文档，每份都会暂停与你讨论 |
| `query [问题]` | 基于 wiki 已有知识回答问题 | `query RAG 和微调的区别是什么？` | 从 wiki 定位相关页面 → 组合回答 → 默认保存到 `wiki/analysis/candidates/`，下次 `lint` 批量审核 |
| `lint` | 对 wiki 进行健康检查 | `lint` | 扫描全部页面 → 报告问题 → 等你批准后修复 |
| `research [topic]` | 启动一次深度研究 | `research 全球出生率下降的原因` | 建 brief → 拉 wiki 背景 → 6 视角分析 → 产出到 projects/ |
| `absorb [topic]` | 将研究产出吸收回 wiki | `absorb global-birth-rate` | 读取 projects/ 产出 → 提取知识 → 回写 wiki |

## 辅助命令

| 命令 | 说明 | 示例 | AI 行为 |
|---|---|---|---|
| `status` | 查看系统当前状态 | `status` | 报告 wiki 页面总数、最近操作、知识覆盖面 |
| `show index` | 查看 wiki 目录 | `show index` | 展示 wiki/index.md 内容 |
| `show log` | 查看最近操作记录 | `show log` | 展示 wiki/log.md 最近 10 条记录 |
| `show overview` | 查看知识库全景 | `show overview` | 展示 wiki/overview.md 内容 |
| `show glossary` | 查看术语表 | `show glossary` | 展示 wiki/glossary.md 内容 |
| `show data-points` | 查看数据积累表 | `show data-points` | 展示 wiki/data-points.md 内容 |
| `show research-log` | 查看研究历史 | `show research-log` | 展示 projects/task-log.md 内容 |
| `list sources` | 列出所有已摄取的文档 | `list sources` | 列出 wiki/sources/ 下所有文件 |
| `list concepts` | 列出所有概念页 | `list concepts` | 列出 wiki/concepts/ 下所有文件 |
| `list entities` | 列出所有实体页 | `list entities` | 列出 wiki/entities/ 下所有文件 |
| `list products` | 列出所有产品页 | `list products` | 列出 wiki/products/ 下所有文件 |
| `list projects` | 列出所有研究项目 | `list projects` | 列出 projects/ 下所有目录 |

## 使用建议

| 场景 | 推荐命令 | 频率 |
|---|---|---|
| 外部云端资料需要进入 Raw | `stage [external source]` | 每次把外部资料封存为本地 Raw 文件时 |
| 刚拿到一份新文档 | `ingest raw/[folder]/[file]` | 每次获得新文档时 |
| 想快速了解某个问题 | `query [问题]` | 随时 |
| 知识库积累了一段时间 | `lint` | 每 10 次 ingest 后 |
| 需要对某个主题做深度分析 | `research [topic]` | 按需 |
| 研究完成，审阅产出后 | `absorb [topic]` | 每次 research 完成后 |
| 忘了知识库里有什么 | `status` 或 `show index` | 每次会话开始时 |
