# 系统使用流程图

> 协议版本:**v2.0**(2026-05-07)。此文件描绘 5 层架构（raw / save / wiki / methods / projects）与 8 个操作（按 Input / Processing / Maintenance 三阶段分组）。完整变更见 `CLAUDE.md` 顶部 Changelog。

## 图1：总体架构

```mermaid
graph TB
    subgraph L1["第一层：raw/ 原始文档层（用户输入区）"]
        direction LR
        R1[meetings/]
        R2[articles/]
        R3[reports/]
        R4[manuscripts/]
        R5[misc/]
    end

    subgraph LS["第二层：save/ 用户主动保存区"]
        direction TB
        S_IDX[index.md\n保存索引]
        S_NOTE[YYYY-MM-DD-slug.md\n对话成果/决策/工作流]
    end

    subgraph L2["第三层：wiki/ 持久知识库（AI 维护）"]
        direction TB
        W_IDX[index.md\n导航中心]
        W_OVW[overview.md\n总览]
        W_GLO[glossary.md\n术语表]
        W_DAT[data-points.md\n数据点]
        W_LOG[log.md\n操作日志]
        subgraph W_SUB["wiki 子目录"]
            direction LR
            WS[sources/\n来源摘要]
            WC[concepts/\n概念]
            WE[entities/\n实体]
            WP[products/\n产品]
            WCM[comparison/\n对比]
            WA[analysis/ + candidates/\n分析 & 候选]
        end
    end

    subgraph L3["第四层：methods/ 研究方法库"]
        direction TB
        M_IDX[index.md\n方法选择器]
        subgraph M_LENS["lens-research/ 六视角分析"]
            direction LR
            LR_IDX[index.md]
            LR_M[methodology/]
            LR_L[lenses/ 六视角]
        end
        subgraph M_DS["deep-search/ 并行深度搜索"]
            direction LR
            DS_IDX[index.md]
            DS_T[templates/\n4 个模板]
        end
        subgraph M_NLM["notebooklm-analysis/ NotebookLM 分析"]
            direction LR
            NLM_IDX[index.md]
            NLM_T[templates/\n5 类产物模板]
        end
    end

    subgraph L4["第五层：projects/ 任务工作区"]
        direction TB
        PLOG[task-log.md\n任务日志]
        subgraph PRJ["projects/[topic]/"]
            direction LR
            P1[brief.md\nmethod: ...]
            P2[按所选方法产出\n不同交付物组合]
        end
    end

    USER((用户))

    USER -- "stage 外部来源暂存" --> EXT["外部来源\nNotion / Drive / Feishu /\n本地外部文件路径 / etc."]
    EXT -- "按内容类型创建\n不可变 Raw Markdown 快照" --> L1
    USER -- "ingest 摄取" --> L1
    USER -- "query 查询" --> L2
    USER -- "save 保存对话资产" --> LS
    USER -- "lint 健康检查" --> L2
    USER -- "research 研究" --> L3
    USER -- "absorb 吸收" --> L4

    L1 -- "ingest 处理后写入" --> L2
    LS -. "可作为上下文\n或后续研究种子" .-> L3
    LS -. "经审阅后可吸收" .-> L2
    L2 -- "research 读取背景" --> L3
    L3 -- "选定方法后执行\n产出写入" --> L4
    L4 -- "absorb 回写（含准入检查）" --> L2

    P2 -. "open-questions\n触发下一轮" .-> P1

    style L1 fill:#FFF3E0,stroke:#FF8C00,color:#000
    style LS fill:#FFFDE7,stroke:#F9A825,color:#000
    style L2 fill:#E8F5E9,stroke:#2E7D32,color:#000
    style L3 fill:#E3F2FD,stroke:#1565C0,color:#000
    style L4 fill:#F3E5F5,stroke:#6A1B9A,color:#000
    style USER fill:#FFEBEE,stroke:#C62828,color:#000
```

---

## 图2：Raw 来源处理流程

### 图2a：stage 外部来源暂存

```mermaid
flowchart TD
    A([用户]) -- "指定外部来源\n云端/本地路径" --> B["AI 读取来源\nNotion / Drive / Feishu /\n本地外部文件路径 / etc."]
    B --> B1{"是否为需转写格式？\nPDF / DOCX / PPTX / XLSX / HTML..."}
    B1 -- "是" --> B2{"MarkItDown 是否可用？"}
    B2 -- "可用" --> B3["用 MarkItDown 转为 Markdown"]
    B2 -- "不可用/失败" --> B4["不中断 stage\n改用 AI fallback / 直接提取 / 元数据快照"]
    B1 -- "否" --> C
    B3 --> C{"按内容类型选择\n现有 raw 子目录"}
    B4 --> C
    C --> D["创建一个新的本地 Raw Markdown\nraw/[content-type]/[file].md"]
    D --> E["写入 Stage Record\nSource Record\nConversion Record(如适用)\nIncluded / Excluded\nContent"]
    E --> F["生成后即视为\n不可变 Raw 原始材料"]
    F --> G([stage 完成\n不更新 wiki])

    style A fill:#FFEBEE,stroke:#C62828,color:#000
    style G fill:#E8F5E9,stroke:#2E7D32,color:#000
    style B fill:#E3F2FD,stroke:#1565C0,color:#000
    style D fill:#FFF3E0,stroke:#FF8C00,color:#000
```

**关键约束**:
- Raw 分类按**内容类型**，不按来源工具(Notion 手稿 → `raw/manuscripts/`，本地 PDF 报告 → `raw/reports/`)
- `stage` 只创建新的 Raw 文件，不更新 wiki
- Connector 选择顺序:**MCP connector → CLI → 浏览器自动化 → 人工导出 → 用户提供副本**(详见 CLAUDE.md `stage` 操作说明)
- 对 PDF / DOCX / PPTX / XLSX / HTML 等本地外部文件，MarkItDown 是优先增强工具；如果本机没有或转换失败，`stage` 不阻断，改用 fallback，并在 `Conversion Record` 里说明完整性与限制
- 转写结果直接写入唯一的 Raw Markdown 文件，不额外保留中间转换稿，除非用户明确要求
- 创建前必须 dedup 检查;同源已存在则询问用户(replace / new versioned snapshot / skip)
- 文件命名:`[YYYY-MM-DD]-[slug](-v[N]).md`;首次快照不带 `-v`，第二次起加 `-v2` `-v3` ...
- 文件创建后视为**不可变**;后续 AI 操作只读不写;后续使用记录写入 `wiki/log.md`、`wiki/sources/` 或项目 source appendix

```mermaid
flowchart TD
    A([用户]) -- "将文档放入 raw/\nmeetings/ articles/\nreports/ manuscripts/" --> B

    B[/"raw/ 原始文档"/]
    B --> C["AI 读取文档内容"]
    C --> C1{"是否为 staged Raw\n且内容是链接索引？"}
    C1 -- "是" --> C2["识别全部外部链接\n请求用户确认 ingest 范围"]
    C2 --> C3{"用户选择"}
    C3 -- "只摄取索引" --> D
    C3 -- "展开链接正文" --> C4["读取已确认范围内\n外部链接内容"]
    C4 --> D
    C1 -- "否" --> D["与用户讨论\n核心要点 & 重要发现"]
    D --> E["创建 wiki/sources/ 摘要页\n一文一页\n（frontmatter: type=fact）"]

    E --> F{"文档包含哪些内容？"}

    F -- "涉及实体\n（人/机构/地点）" --> G["更新 wiki/entities/\ntype: fact"]
    F -- "涉及产品\n（工具/系统/方案）" --> H["更新 wiki/products/\ntype: fact/interpretation"]
    F -- "涉及概念\n（理论/框架/思想）" --> I["更新 wiki/concepts/\ntype: fact/interpretation/opinion"]

    G & H & I --> J["更新 wiki/glossary.md\n新增或修订术语定义"]
    J --> K["更新 wiki/data-points.md\n每条附来源"]
    K --> L["更新 wiki/overview.md\n修订知识库总览"]
    L --> M["更新 wiki/index.md\n添加导航条目"]
    M --> N["记录 wiki/log.md\n写入操作时间戳与摘要"]
    N --> O([ingest 完成])

    style A fill:#FFEBEE,stroke:#C62828,color:#000
    style O fill:#E8F5E9,stroke:#2E7D32,color:#000
    style B fill:#FFF3E0,stroke:#FF8C00,color:#000
    style F fill:#FFF9C4,stroke:#F9A825,color:#000
```

**关键约束**:每个新页必须带 5 字段 frontmatter（id / domain / type / source_lineage / last_updated）。无可追溯来源证据不得标 `fact`。manuscripts 默认标 `opinion`。LLM 生成的 raw 文件不构成 fact 证据，除非由非 LLM 一手或权威来源独立验证（详见 CLAUDE.md `Fact Promotion Rules` 段）。

---

## 图3：query 查询操作（候选晶化流程）

```mermaid
flowchart TD
    A([用户提问]) --> B["AI 读取 wiki/index.md\n定位相关页面"]
    B --> C["读取相关 wiki 页面\nsources/ concepts/ entities/\nproducts/ comparison/ analysis/"]
    C --> D["组合回答\n尊重每页的 type 级别\nhypothesis 不支撑 fact 结论"]
    D --> E["向用户呈现回答"]
    E --> F["默认保存到\nwiki/analysis/candidates/\nstatus: candidate\n（不打断用户）"]
    F --> G["提示：下次 lint 批量审阅"]
    G --> H([查询结束])

    style A fill:#FFEBEE,stroke:#C62828,color:#000
    style H fill:#E8F5E9,stroke:#2E7D32,color:#000
    style F fill:#FFECB3,stroke:#F9A825,color:#000
```

---

## 图3b：save 保存操作（用户主动收藏流程）

```mermaid
flowchart TD
    A([用户明确要求保存]) --> B["AI 判断保存范围\n当前讨论 / 决策 / 工作流 / prompt"]
    B --> C{"范围是否清楚\n且不含敏感误收内容？"}
    C -- "不清楚" --> C1["向用户确认\n保存边界"]
    C1 --> D
    C -- "清楚" --> D["选择文件名\nsave/YYYY-MM-DD-slug.md"]
    D --> E{"是否已有相近保存笔记？"}
    E -- "有" --> E1["询问：新建 / 版本化 / 跳过"]
    E1 --> F
    E -- "无" --> F["写入保存笔记\nSave Record + Content\n+ Possible Next Actions"]
    F --> G["更新 save/index.md\n添加链接和一句话说明"]
    G --> H{"用户是否要求\n同步到外部地点？"}
    H -- "否" --> I["向用户返回保存路径\n说明后续可 query / research / absorb"]
    H -- "是" --> J["执行 save-sync\n选择连接器/API/CLI/人工导出"]
    J --> K["创建外部副本\nGoogle Drive / Notion / 其他位置"]
    K --> L["记录 Sync Record\n外部链接/路径/状态"]
    L --> M["向用户返回\n本地路径 + 外部位置"]
    I --> N([save 完成])
    M --> N

    style A fill:#FFEBEE,stroke:#C62828,color:#000
    style N fill:#E8F5E9,stroke:#2E7D32,color:#000
    style F fill:#FFFDE7,stroke:#F9A825,color:#000
    style C fill:#FFF9C4,stroke:#F9A825,color:#000
    style E fill:#FFF9C4,stroke:#F9A825,color:#000
    style H fill:#FFF9C4,stroke:#F9A825,color:#000
```

**关键约束**:
- `save/` 只保存用户明确要求保存的对话资产，不自动捕获所有对话
- 外部原始资料仍走 `stage → raw/`
- 结构化研究仍走 `research → projects/`
- 长期知识沉淀仍需审阅后进入 `wiki/`
- `save/` 内容本身不构成 `type: fact` 证据
- 外部同步是单向副本：`save/` → Google Drive / Notion / 其他位置
- 本地 `save/` 始终是主记录；v1 不处理外部回写和 merge

---

## 图4：lint 健康检查操作（结构 + 候选审阅）

```mermaid
flowchart TD
    A([用户触发 lint]) --> B["AI 扫描全部 wiki/ 页面"]

    B --> C{"Part A\n结构健康检查"}

    C -- "检查①" --> D["frontmatter 缺失/格式错误\n5 字段是否齐全？"]
    C -- "检查②" --> D2["type=fact 却无 source_lineage\n（违规，必须降级或补源）"]
    C -- "检查③" --> E["内容矛盾\n不同页面存在冲突叙述"]
    C -- "检查④" --> F["过时信息\n事实已被新文档推翻"]
    C -- "检查⑤" --> G["孤立页面 / 断链 / 术语不一致"]

    D & D2 & E & F & G --> I["汇总 Part A 问题报告"]

    B --> P["Part B\n候选队列审阅\nwiki/analysis/candidates/"]
    P --> Q{"每个候选\n用户决策"}
    Q -- "Promote" --> Q1["移入 wiki/analysis/\n去掉 status:candidate\n注册到 index"]
    Q -- "Keep" --> Q2["保留为候选\n下次再审"]
    Q -- "Discard" --> Q3["删除"]

    I --> J["向用户展示报告"]
    Q1 & Q2 & Q3 --> J
    J --> K{"用户是否\n批准修复？"}

    K -- "否，仅查看" --> L([lint 结束，不修改])
    K -- "是，执行修复" --> M["AI 修复已批准的问题"]
    M --> N["记录 wiki/log.md"]
    N --> O([lint 完成])

    style A fill:#FFEBEE,stroke:#C62828,color:#000
    style L fill:#FFF3E0,stroke:#FF8C00,color:#000
    style O fill:#E8F5E9,stroke:#2E7D32,color:#000
    style C fill:#FFF9C4,stroke:#F9A825,color:#000
    style Q fill:#FFF9C4,stroke:#F9A825,color:#000
    style K fill:#FFF9C4,stroke:#F9A825,color:#000
```

---

## 图5：research 研究操作（方法选择 + 分支执行）

```mermaid
flowchart TD
    A([用户提出研究问题]) --> B["读取 methods/index.md\n方法选择器"]
    B --> C{"问题形状判断\n1. 解释型还是事实型?\n2. 交叉验证需求?\n3. 期望产出形态?"}

    C -- "解释型 / 重深度\n→ lens-research" --> LR_FLOW
    C -- "事实型 / 重验证\n→ deep-search" --> DS_FLOW
    C -- "明确需要 NotebookLM\n→ notebooklm-analysis" --> NLM_FLOW

    subgraph LR_FLOW["lens-research 执行流"]
        direction TB
        LR1["读 wiki/index.md\n拉取背景"] --> LR2["写 brief.md\nmethod: lens-research"]
        LR2 --> LR3["依序执行 6 个视角\ntechnical → economic → historical\n→ geopolitical → contrarian\n→ first-principles"]
        LR3 --> LR4["解决矛盾\ncontradiction-protocol"]
        LR4 --> LR5["综合\nsynthesis-rules"]
        LR5 --> LR6["产出 4 个文件:\nexec-summary / deep-dive\n/ key-players / open-questions"]
    end

    subgraph DS_FLOW["deep-search 执行流"]
        direction TB
        DS1["读 wiki/index.md\n拉取背景"] --> DS2["写 brief.md\nmethod: deep-search\n决定 5-8 个 angles"]
        DS2 --> DS3["Phase 1: 并行启动\n5-8 个 sub-agent\n每个一个 angle"]
        DS3 --> DS4["Phase 2: 共识矩阵\nCONSENSUS/STRONG\nDISPUTED/UNVERIFIED\nCONTRADICTED"]
        DS4 --> DS5["Phase 3: 苏格拉底质询\n5 个维度\n修订置信度"]
        DS5 --> DS6["产出 6 个文件:\nexec-summary / deep-dive\n/ consensus-matrix\n/ socratic-review\n/ source-appendix\n/ open-questions"]
    end

    subgraph NLM_FLOW["notebooklm-analysis 执行流"]
        direction TB
        NLM1["读 wiki/index.md\n拉取背景"] --> NLM2["写 brief.md\nmethod: notebooklm-analysis"]
        NLM2 --> NLM3["连接 NotebookLM\n选择/创建 notebook\n读取 source list"]
        NLM3 --> NLM4["Source diff\nbaseline / no-new-source\n/ incremental update"]
        NLM4 --> NLM5{"是否有新增 source？"}
        NLM5 -- "无新增" --> NLM6["只更新 00-index\nmetadata / run-log"]
        NLM5 -- "有新增" --> NLM7["按 templates 生成 5 类产物\nsource-summary / insights\ntrend / questions / concept-map"]
        NLM6 --> NLM8["可选同步 Google Drive\n记录 drive-links"]
        NLM7 --> NLM8
    end

    LR6 --> LOG["追加一条记录到\nprojects/task-log.md\nMethod 字段标记所用方法"]
    DS6 --> LOG
    NLM8 --> LOG
    LOG --> END_R([research 完成])

    OQ_NOTE["open-questions.md\n触发下一轮 research"] -. "作为新问题的种子" .-> A

    style A fill:#FFEBEE,stroke:#C62828,color:#000
    style END_R fill:#E8F5E9,stroke:#2E7D32,color:#000
    style C fill:#FFF9C4,stroke:#F9A825,color:#000
    style LR_FLOW fill:#E3F2FD,stroke:#1565C0,color:#000
    style DS_FLOW fill:#E1F5FE,stroke:#0277BD,color:#000
    style NLM_FLOW fill:#EDE7F6,stroke:#512DA8,color:#000
    style LOG fill:#F3E5F5,stroke:#6A1B9A,color:#000
```

---

## 图6：absorb 吸收操作（知识闭环 + 准入检查）

```mermaid
flowchart TD
    A([用户触发 absorb]) --> B["AI 读取 projects/[topic]/\n全部交付物"]

    B --> ADM{"Step 0: 准入检查\n（4 项）"}
    ADM -- "① Lineage\n每条 claim 可溯源?" --> ADM
    ADM -- "② Consistency\n是否与既有 fact 页矛盾?" --> CON["矛盾 → 不合并\n记入 overview.md\nopen-contradictions"]
    ADM -- "③ Structure\nfrontmatter 合规?" --> ADM
    ADM -- "④ Domain\ndomain 标签匹配?" --> ADM

    ADM -- "全部通过" --> C{"从研究产出中提取"}
    CON -.-> ADM

    C -- "新概念" --> D["写入 wiki/concepts/\n新建或更新概念页"]
    C -- "新实体" --> E["写入 wiki/entities/\n新建或更新实体页"]
    C -- "新产品" --> F["写入 wiki/products/\n新建或更新产品页"]
    C -- "数据与事实" --> G["写入 wiki/data-points.md\n追加数据条目"]
    C -- "对比分析" --> H["写入 wiki/comparison/\n新建或更新对比页"]

    D & E & F & G & H --> I["更新 wiki/glossary.md\n添加新术语定义"]
    I --> J["更新 wiki/overview.md\n反映新知识格局"]
    J --> K["更新 wiki/index.md\n添加新页面导航"]
    K --> L["记录 wiki/log.md\n含准入门挡下的延迟项"]
    L --> M([absorb 完成\n知识已回写 wiki])

    M -. "wiki 更新支撑\n下一次 research" .-> N[(wiki/\n持续增长)]

    style A fill:#FFEBEE,stroke:#C62828,color:#000
    style M fill:#E8F5E9,stroke:#2E7D32,color:#000
    style N fill:#E8F5E9,stroke:#2E7D32,color:#000
    style C fill:#FFF9C4,stroke:#F9A825,color:#000
    style ADM fill:#FFCCBC,stroke:#D84315,color:#000
    style CON fill:#FFCDD2,stroke:#C62828,color:#000
```
