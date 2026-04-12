# karpathy-llm-wiki

**LLM Wiki skill for building a Karpathy-style personal knowledge base with Claude Code, Cursor, Codex, and other Agent Skills tools**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/Astro-Han/karpathy-llm-wiki?style=social)](https://github.com/Astro-Han/karpathy-llm-wiki)
[![GitHub forks](https://img.shields.io/github/forks/Astro-Han/karpathy-llm-wiki?style=social)](https://github.com/Astro-Han/karpathy-llm-wiki)
[![Agent Skills](https://img.shields.io/badge/Agent_Skills-compatible-blue)](https://agentskills.io)
[![Install](https://img.shields.io/badge/Install-npx_add--skill-green)](https://github.com/Astro-Han/karpathy-llm-wiki#install)

<p align="center">
  <img src="assets/karpathy-tweet.png" alt="Karpathy's tweet about LLM Wiki" width="560">
</p>

`karpathy-llm-wiki` is an Agent Skills-compatible `SKILL.md` package for building a markdown-first LLM wiki. It helps coding agents ingest source material into `raw/`, compile durable knowledge pages into `wiki/`, answer questions with citations, and lint the wiki for consistency. If you are looking for an LLM wiki, a Karpathy wiki workflow, a personal knowledge base for coding agents, or a RAG alternative based on compiled markdown, this repository is built for that use case.

## Key Takeaways

- **Direct answer**: this repo packages Karpathy's LLM Wiki workflow into one reusable skill
- **Three operations**: Ingest sources, Query your wiki with citations, Lint for consistency
- **Cross-tool support**: works with Claude Code, Cursor, Codex CLI, OpenCode, and other Agent Skills tools
- **Real usage proof**: based on a production knowledge base with 94 articles, 99 sources, and daily use since April 2026

## What Is an LLM Wiki?

An **LLM wiki** is a knowledge system where the LLM maintains a structured wiki instead of re-searching raw documents from scratch on every question. New sources are compiled into durable markdown pages, cross-references are updated over time, and answers cite the wiki pages that already contain the synthesized knowledge.

This repository turns that idea into a reusable skill:

| Operation | What it does | Output |
|-----------|--------------|--------|
| **Ingest** | Collects a source into `raw/` and compiles it into the wiki | New or updated wiki pages |
| **Query** | Searches the wiki and answers with citations | Grounded answers that link back to markdown pages |
| **Lint** | Checks index integrity, links, and wiki health | Auto-fixes plus reported issues |

See [SKILL.md](SKILL.md) for the full skill behavior and [docs/SPEC.md](docs/SPEC.md) for the design rationale.

## LLM Wiki vs RAG

People often search for **LLM wiki vs RAG**, **personal knowledge base for agents**, or **markdown knowledge base**. The difference is simple:

| Approach | Knowledge lives in | When synthesis happens | Good for |
|----------|--------------------|------------------------|----------|
| **RAG** | Raw chunks and embeddings | At query time | Broad retrieval across large corpora |
| **LLM Wiki** | Curated markdown pages | During ingest and maintenance | Compounding knowledge, summaries, comparisons, and durable cross-links |

`karpathy-llm-wiki` is optimized for the second model. It is most useful when you want a knowledge base that improves over time instead of re-deriving the same relationships on every query.

## Proof From Real Usage

This skill is based on a production knowledge base that has been maintained daily since April 2026:

| Metric | Count | Detail |
|--------|-------|--------|
| Wiki articles | **94** | Grew from 48 to 94 |
| Source materials | **99** | Started from 0 |
| Topic directories | **13** | Grew from 8 to 13 |
| Operation log entries | **87** | Recorded in the wiki log |
| Recent 7-day activity | **87 entries** | Active ongoing maintenance |

Top topics include **ai-coding-tools** with 29 articles, **ai-research** with 24 articles, and **product-design-frameworks** with 8 articles.

See [examples/](examples/) for example wiki pages, source files, and operation logs.

## Install

```bash
npx add-skill Astro-Han/karpathy-llm-wiki
```

This install path works with tools that support the [Agent Skills](https://agentskills.io) standard.

## Quick Start

### 1. Ingest your first source

Give the skill a URL, a file, or pasted text:

> "Ingest this article: https://example.com/attention-is-all-you-need"

The skill stores the source in `raw/`, then compiles or updates the right knowledge pages in `wiki/`.

### 2. Ask your wiki a question

> "What do I know about attention mechanisms?"

The skill searches the wiki and answers with citations linking back to your markdown pages.

### 3. Keep the wiki healthy

> "Lint my wiki"

The skill checks for broken links, missing index entries, stale cross-references, and related consistency issues.

## Who This Skill Is For

This project is designed for people who:

- want to try Karpathy's LLM Wiki idea without building a custom system first
- want a **personal knowledge base** powered by Claude Code, Cursor, Codex, or similar tools
- prefer a **markdown-first workflow** over a hosted vector database
- want an **Agent Skills** package they can install and reuse across coding tools

## How the Karpathy-Style Workflow Works

Karpathy's core idea is that the LLM should maintain the wiki while the human focuses on choosing sources and asking good questions.

This skill follows that model with a simple file layout:

```text
your-project/
├── raw/            ← Immutable source material
│   └── topic/
│       └── 2026-04-03-source-article.md
├── wiki/           ← Compiled knowledge pages maintained by the LLM
│   ├── topic/
│   │   └── concept-name.md
│   ├── index.md    ← Global table of contents
│   └── log.md      ← Append-only operation log
```

Each new source can update multiple pages, strengthen cross-references, and record contradictions or revisions. That is what makes the wiki compound over time.

## Tool Compatibility

This skill follows the [agentskills.io](https://agentskills.io) open standard and works with any LLM coding tool that supports `SKILL.md`:

| Tool | Install method |
|------|----------------|
| Claude Code | `npx add-skill Astro-Han/karpathy-llm-wiki` |
| Cursor | `npx add-skill Astro-Han/karpathy-llm-wiki` |
| Codex CLI | Copy to `.agents/skills/karpathy-llm-wiki/` |
| OpenCode | `npx add-skill Astro-Han/karpathy-llm-wiki` |
| Other tools | Copy `SKILL.md` and `references/` into the tool's skill directory |

## FAQ

### What is the difference between an LLM wiki and a personal wiki?

An LLM wiki is maintained by the model. The LLM updates summaries, cross-links, index entries, and contradictions as new material arrives. A normal personal wiki usually depends on manual editing.

### How is this different from RAG?

RAG retrieves raw chunks at query time. An LLM wiki compiles source material into durable pages first, then answers from those pages. That makes it easier to maintain evolving summaries, topic pages, and explicit cross-references.

### Which tools are compatible?

Claude Code, Cursor, Codex CLI, OpenCode, and other tools that support the Agent Skills `SKILL.md` standard.

### What sources can I ingest?

Web pages, papers, blog posts, PDFs, markdown files, text files, and pasted text. The skill converts source material into markdown under `raw/` and compiles it into `wiki/`.

### Is this production-ready?

The workflow in this repo is based on a real knowledge base with 94 articles and 99 sources that has been maintained daily since April 2026. The repo also includes examples, templates, and a documented design spec.

## Inspired By

This is an unofficial community implementation of the workflow described in [Karpathy's LLM Wiki idea file](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f). The value of this repository is the reusable workflow, prompt structure, and battle-tested knowledge-compilation rules.

Alternative implementations include [lucasastorian/llmwiki](https://github.com/lucasastorian/llmwiki) and [atomicmemory/llm-wiki-compiler](https://github.com/atomicmemory/llm-wiki-compiler).

## License

[MIT](LICENSE)
