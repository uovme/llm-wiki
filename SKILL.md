---
name: karpathy-llm-wiki
description: "Use when building or maintaining a personal LLM-powered knowledge base. Triggers: ingesting sources into a wiki, querying wiki knowledge, reflecting on topics, linting wiki quality, 'add to wiki', 'what do I know about', or any mention of 'LLM wiki' or 'Karpathy wiki'."
---

# Karpathy LLM Wiki

Build and maintain a personal knowledge base using LLMs.

Core ideas from Karpathy:
- "The LLM writes and maintains the wiki; the human reads and asks questions."
- "The wiki is a persistent, compounding artifact."

## Purpose

本库采用 topic-centric 的 LLM Wiki 结构。`RAW/` 保存原始资料，`Wiki/` 保存由 LLM 维护的知识页与 workflow 产物。

## Directory Contract

### Raw Layer

- `RAW/`：原始资料与中间专题整理层。
- 默认只读；除收集与规范化存档外，不回写原始内容。
- 按主题子目录组织，例如 `RAW/machine-learning/`。
- 文件命名：`YYYY-MM-DD-descriptive-slug.md`（日期未知则省略前缀）。

See `references/raw-template.md` for the exact format.

### Wiki Root

- `Wiki/purpose.md`：Wiki 的灵魂。定义目标、关键问题、研究范围与演进论点。Agent 在每次 ingest 和 query 时读取，确保产出方向一致。See `references/purpose-template.md`.
- `Wiki/overview.md`：全库自动更新的全局摘要。每次 ingest 后刷新，提供比 index.md（目录表格）更适合快速理解全局上下文的叙述性概览。See `references/overview-template.md`.
- `Wiki/index.md`：全库目录与主题入口。
- `Wiki/log.md`：全库按时间记录的操作日志。
- `Wiki/Query/`：跨主题 query 归档与值得保存的问答快照。
- `Wiki/Lint/`：跨主题 / 全库级 lint 报告。

### Topic Layout

每个主题位于 `Wiki/<topic>/`，例如 `Wiki/GH3539/`。

其中分为四类内容：

1. **living pages**：直接放在 `Wiki/<topic>/` 根下。主题的核心知识页面，持续更新。
2. **ingest records**：放在 `Wiki/<topic>/ingest/`。每次 ingest 操作的记录。
3. **reflection pages**：放在 `Wiki/<topic>/reflect/`。长期判断与综合分析。
4. **topic lint reports**：放在 `Wiki/<topic>/lint/`。主题内质量检查报告。

Templates live in `references/` relative to this file. Read them when you need the exact format.

### Initialization

Triggers only on the first Ingest. Check whether `RAW/` and `Wiki/` exist. Create only what is missing; never overwrite existing files:

- `RAW/` directory (with `.gitkeep`)
- `Wiki/` directory (with `.gitkeep`)
- `Wiki/purpose.md` — See `references/purpose-template.md`. Ask the user to fill in goals and scope, or generate a draft based on the first source.
- `Wiki/overview.md` — heading `# Wiki Overview`, one-line placeholder
- `Wiki/index.md` — heading `# Knowledge Base Index`, empty body
- `Wiki/log.md` — heading `# Wiki Log`, empty body
- `Wiki/Query/` directory (with `.gitkeep`)
- `Wiki/Lint/` directory (with `.gitkeep`)

If Query, Reflect, or Lint cannot find the wiki structure, tell the user: "Run an ingest first to initialize the wiki." Do not auto-create.

---

## Workflow Contract

### Ingest

从 `RAW/` 读取新来源，更新 Wiki 知识页。

#### Fetch (RAW/)

1. Get the source content using whatever web or file tools your environment provides. If nothing can reach the source, ask the user to paste it directly.

2. Pick a topic directory. Check existing `RAW/` subdirectories first; reuse one if the topic is close enough. Create a new subdirectory only for genuinely distinct topics.

3. Save as `RAW/<topic>/YYYY-MM-DD-descriptive-slug.md`.
   - Slug from source title, kebab-case, max 60 characters.
   - Published date unknown → omit the date prefix from the file name. The metadata Published field still appears; set it to `Unknown`.
   - If a file with the same name already exists, append a numeric suffix (e.g., `descriptive-slug-2.md`).
   - Include metadata header: source URL, collected date, published date.
   - Preserve original text. Clean formatting noise. Do not rewrite opinions.

   See `references/raw-template.md` for the exact format.

#### Compile (Wiki/)

先读取 `Wiki/purpose.md` 以理解 Wiki 的方向与关注点，再更新 `Wiki/<topic>/` 下的 living pages：

- **Same core thesis as existing page** → Merge into that page. Add the new source to Sources/Raw. Update affected sections.
- **New concept** → Create a new living page in the most relevant topic directory. Name the file after the concept, not the raw file.
- **Spans multiple topics** → Place in the most relevant directory. Add See Also cross-references to related pages elsewhere.

These are not mutually exclusive. A single source may warrant merging into one page while also creating a separate page for a distinct concept it introduces. In all cases, check for factual conflicts: if the new source contradicts existing content, annotate the disagreement with source attribution.

See `references/article-template.md` for living page format.

#### Post-Ingest

1. 写一条 ingest 记录到 `Wiki/<topic>/ingest/`。See `references/ingest-template.md` for format.
2. 更新 `Wiki/overview.md`：重新生成全局摘要，反映 Wiki 当前整体知识状态。See `references/overview-template.md`.
3. 更新 `Wiki/index.md`（如目录结构或页面集合发生变化）。See `references/index-template.md` for format.
4. 追加一条日志到 `Wiki/log.md`：

```
## [YYYY-MM-DD] ingest | <primary page title>
- Source: <raw file name>
- Updated: <updated page title>
```

Omit `- Updated:` lines when no additional pages are updated.

---

### Query

优先从 `Wiki/index.md` 与现有 Wiki 页面回答。

#### Steps

1. Read `Wiki/purpose.md` to understand the Wiki's goals and scope.
2. Read `Wiki/index.md` to locate relevant pages.
3. Read those pages and synthesize an answer.
4. Prefer wiki content over your own training knowledge. Cite sources with Obsidian wikilinks: `[[page name]]`.
5. Output the answer in the conversation. Do not write files unless asked.

#### Archiving

只有用户要求保存时，才把问答结果写入 `Wiki/Query/`。

Query 归档是 point-in-time snapshot，不应直接替代 living pages。

1. Write the answer as a new page in `Wiki/Query/`. See `references/query-template.md`. File name reflects the query topic, e.g., `transformer-architectures-overview.md`.
2. Always create a new page. Never merge into existing living pages.
3. Update `Wiki/index.md`. Prefix the Summary with `[Query]`.
4. Append to `Wiki/log.md`:
   ```
   ## [YYYY-MM-DD] query | Archived: <page title>
   ```

---

### Reflect

将长期判断、可持续更新的综合页写入 `Wiki/<topic>/reflect/`。

#### When to Reflect

- User explicitly requests a reflection or synthesis.
- A topic has accumulated enough living pages to warrant a higher-level summary.

#### Steps

1. 当现有 reflect 页面主题一致时，应优先更新而不是新建重复页面。
2. reflect 页面需要显式链接回对应 living pages 与关键 RAW。
3. Use `references/reflect-template.md` for format.
4. Update `Wiki/index.md`. Prefix the Summary with `[Reflect]`.
5. Append to `Wiki/log.md`:
   ```
   ## [YYYY-MM-DD] reflect | <page title>
   ```

---

### Lint

Quality checks on the wiki. Two categories with different authority levels.

#### Deterministic Checks (auto-fix)

自动修复确定性问题：

**Index consistency** — compare `Wiki/index.md` against actual `Wiki/` files:
- File exists but missing from index → add entry with `(no summary)` placeholder.
- Index entry points to nonexistent file → mark as `[MISSING]` in the index. Do not delete the entry; let the user decide.

**Internal links** — for every wikilink in `Wiki/` page files:
- Target does not exist → search `Wiki/` for a page with the same name elsewhere.
  - Exactly one match → fix the link.
  - Zero or multiple matches → report to the user.

**Raw references** — every link to `RAW/` must point to an existing file:
- Target does not exist → search `RAW/` for a file with the same name elsewhere.
  - Exactly one match → fix the path.
  - Zero or multiple matches → report to the user.

#### Heuristic Checks (report only)

对判断性问题做清晰记录，不静默覆盖：

- Factual contradictions across pages
- Outdated claims superseded by newer sources
- Missing conflict annotations where sources disagree
- Orphan pages with no inbound links from other wiki pages
- Missing cross-topic references
- Concepts frequently mentioned but lacking a dedicated page

#### Post-Lint

- 主题内 lint：写入 `Wiki/<topic>/lint/`。
- 全库 / 跨主题 lint：写入 `Wiki/Lint/`。
- Append to `Wiki/log.md`:
  ```
  ## [YYYY-MM-DD] lint | <N> issues found, <M> auto-fixed
  ```

---

## Link and Naming Rules

- 保持 vault 实际大小写：本库使用 `Wiki/`，不是 `wiki/`；使用 `RAW/`，不是 `raw/`。
- 以 Obsidian wikilink 为主：`[[page name]]`。
- 链接应优先指向实际存在的 `Wiki/...` 路径。
- living page、reflect、ingest、lint 各自按其目录语义放置，不混放。
- Today's date for log entries, Collected dates, and Created dates.
- Published dates come from the source (use `Unknown` when unavailable).

## Maintenance Rules

- 优先增量修改，避免大规模无必要重写。
- 不覆盖用户已有内容，除非明确知道这是当前结构调整的一部分。
- 有冲突证据时，显式保留张力与边界条件。
- `Wiki/log.md` 负责记录发生了什么；具体细节进入相应 ingest / reflect / lint 页面。
