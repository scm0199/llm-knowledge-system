# 用户命令参考

> 协议版本:**v2.0**(2026-05-07)。完整变更见 `CLAUDE.md` 顶部 Changelog。
>
> v2.0 把 8 个核心操作按阶段分组（Input / Processing / Maintenance），并新增 `stage` vs `save-sync` 方向对比，避免随操作数增长导致的命令面板失序。

## 核心操作命令

操作按所处阶段分组。分组只用于引导，每个命令仍按自身名字独立触发。

### 阶段 1 — Input（把材料带入系统）

| 命令 | 说明 | 示例 | AI 行为 |
|---|---|---|---|
| `stage [external source]` | 将外部来源封存为本地 Raw Markdown；本地 PDF/Office 等文件会优先用 MarkItDown 转写 | `stage Notion 中某手稿到 raw/manuscripts/`；`stage /Users/me/Downloads/report.pdf` | 选 connector(优先 MCP → CLI → 浏览器 → 人工)或读取本地路径 → 如需转换则检查 MarkItDown：有则转 Markdown，无则 AI fallback 处理且不中断 → 检查 dedup → 按内容类型选目录 → 用 `[YYYY-MM-DD]-[slug](-v[N]).md` 命名 → 写入 Stage Record / Source Record / Conversion Record(如适用) / Content → 只生成一个 Raw Markdown 文件 → 不更新 wiki;生成后不可变 |
| `save [内容/主题]` | 保存用户指定的对话成果 | `save 刚才关于 save 层的设计` | 判断保存范围 → 写入 `save/YYYY-MM-DD-[slug].md` → 更新 `save/index.md` → 不直接进入 wiki |
| `save-sync [地点] [内容/路径]` | 保存并同步到外部库 | `save-sync Google Drive 刚才的工作流` | 先写入或读取本地 `save/` → 单向同步到外部地点 → 记录外部链接；本地仍为主记录 |

### 阶段 2 — Processing（把材料转成知识）

| 命令 | 说明 | 示例 | AI 行为 |
|---|---|---|---|
| `ingest [path]` | 摄取一份原始文档到 wiki | `ingest raw/articles/llm-wiki.md` | 读取文档；若 staged Raw 是链接索引，先确认只摄取索引还是展开链接 → 与你讨论要点 → 创建/更新 wiki 多个页面 |
| `ingest raw/[folder]/` | 批量摄取某个子目录下所有文档 | `ingest raw/meetings/` | 逐一处理目录下每份文档，每份都会暂停与你讨论 |
| `query [问题]` | 基于 wiki 已有知识回答问题 | `query RAG 和微调的区别是什么？` | 从 wiki 定位相关页面 → 组合回答 → 默认保存到 `wiki/analysis/candidates/`，下次 `lint` 批量审核 |
| `research [topic]` | 启动一次深度研究 | `research 全球出生率下降的原因` | 读取 `methods/index.md` 选择方法 → 建 brief → 拉 wiki 背景 → 按所选方法产出到 `projects/` |

### 阶段 3 — Maintenance（保持知识库健康）

| 命令 | 说明 | 示例 | AI 行为 |
|---|---|---|---|
| `lint` | 对 wiki 进行健康检查 + 候选答案批量审核 + `save/` 错位检查 | `lint` | 扫描全部页面 → 检查 `save/` 是否有应进 raw / candidate / projects 的错位文件 → 报告问题 → 等你批准后修复 |
| `absorb [topic]` | 将研究产出吸收回 wiki | `absorb global-birth-rate` | 读取 projects/ 产出 → 提取知识 → 回写 wiki |

## 方向对比：`stage` 与 `save-sync`

两个命令都涉及外部系统，看起来像但方向相反。混用会导致放错位置。

| | `stage` | `save-sync` |
|---|---|---|
| 方向 | 外部 → 本地 | 本地 → 外部 |
| 主记录 | 新建的本地 Raw 文件 | 本地 `save/` 文件 |
| 用途 | 把外部资料封存为不可变证据 | 把已保存的本地笔记发布/备份到外部 |
| 目标可变性 | Raw 创建后不可变 | 外部副本是衍生件，不回写本地 |
| 触发语境 | "把这份 Notion / PDF / Drive 文档暂存进来" | "保存一下并同步到 Drive / Notion" |

简单记忆：**带进来用 `stage`，发出去用 `save-sync`**。

## 辅助命令

| 命令 | 说明 | 示例 | AI 行为 |
|---|---|---|---|
| `status` | 查看系统当前状态 | `status` | 报告 wiki 页面总数、最近操作、知识覆盖面 |
| `show index` | 查看 wiki 目录 | `show index` | 展示 wiki/index.md 内容 |
| `show log` | 查看最近操作记录 | `show log` | 展示 wiki/log.md 最近 10 条记录 |
| `show overview` | 查看知识库全景 | `show overview` | 展示 wiki/overview.md 内容 |
| `show glossary` | 查看术语表 | `show glossary` | 展示 wiki/glossary.md 内容 |
| `show data-points` | 查看数据积累表 | `show data-points` | 展示 wiki/data-points.md 内容 |
| `show saves` | 查看保存索引 | `show saves` | 展示 save/index.md 内容 |
| `show research-log` | 查看研究历史 | `show research-log` | 展示 projects/task-log.md 内容 |
| `list sources` | 列出所有已摄取的文档 | `list sources` | 列出 wiki/sources/ 下所有文件 |
| `list concepts` | 列出所有概念页 | `list concepts` | 列出 wiki/concepts/ 下所有文件 |
| `list entities` | 列出所有实体页 | `list entities` | 列出 wiki/entities/ 下所有文件 |
| `list products` | 列出所有产品页 | `list products` | 列出 wiki/products/ 下所有文件 |
| `list saves` | 列出所有保存笔记 | `list saves` | 列出 save/ 下除 index.md 外的所有文件 |
| `list projects` | 列出所有研究项目 | `list projects` | 列出 projects/ 下所有目录 |

## 使用建议

| 场景 | 推荐命令 | 频率 |
|---|---|---|
| 外部云端资料需要进入 Raw | `stage [external source]` | 每次把外部资料封存为本地 Raw 文件时 |
| 本地外部 PDF / Word / PPT / Excel 等文件需要进入 Raw | `stage /absolute/path/to/file.pdf` | 每次要把不在知识库目录下的文件转成 Raw Markdown 快照时 |
| 刚拿到一份新文档 | `ingest raw/[folder]/[file]` | 每次获得新文档时 |
| 想快速了解某个问题 | `query [问题]` | 随时 |
| 想保存一段对话成果、决策或工作流 | `save [内容/主题]` | 每次你明确说“保存一下”时 |
| 想保存并同步到 Google Drive / Notion / 其他位置 | `save-sync [地点] [内容/路径]` | 需要外部副本或分享时 |
| 知识库积累了一段时间 | `lint` | 每 10 次 ingest 后 |
| 需要对某个主题做深度分析 | `research [topic]` | 按需 |
| 研究完成，审阅产出后 | `absorb [topic]` | 每次 research 完成后 |
| 忘了知识库里有什么 | `status` 或 `show index` | 每次会话开始时 |
