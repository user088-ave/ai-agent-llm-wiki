# 知識庫異動日誌 (Changelog & Event Log)

本文件為追加式（Append-only）日誌，記錄知識庫自建立以來的所有攝取（Ingest）、問答沉澱（Query Archive）與健康檢查（Lint）歷程。

---

## [2026-11-23] init | 知識庫架構與規範初始化
- 確立「簡單實作 AI Agents」知識庫架構（基於 Karpathy LLM Wiki 構想）。
- 建立 [AGENTS.md](../AGENTS.md) 作為 Schema 與維護標準規範。
- 建立目錄分類：`wiki/sources/`、`wiki/concepts/`、`wiki/patterns/`、`wiki/guides/`。
- 初始化總目錄 [index.md](./index.md) 與日誌 [log.md](./log.md)。
- 準備就緒，待執行 raw/ 5 篇文獻的 Ingest 工作流。

---

## [2026-11-23] ingest | 批次攝取 raw/ 5 篇核心文獻至 Wiki
- 完成 5 篇 raw 文獻提煉，建立 sources 頁面：
  - [01_openai_practical_guide.md](./sources/01_openai_practical_guide.md)
  - [02_build_ai_agent_from_scratch.md](./sources/02_build_ai_agent_from_scratch.md)
  - [03_anatomy_of_an_ai_agent.md](./sources/03_anatomy_of_an_ai_agent.md)
  - [04_react_agent_pattern.md](./sources/04_react_agent_pattern.md)
  - [05_ai_agent_guardrails_vs_evals.md](./sources/05_ai_agent_guardrails_vs_evals.md)
- 合流提煉 4 篇核心概念頁面（concepts/）：
  - [agent-architecture.md](./concepts/agent-architecture.md)（含極簡派 vs 認知架構派權衡）
  - [tool-use-and-registry.md](./concepts/tool-use-and-registry.md)（含工具豐富 vs 精簡權衡）
  - [memory-systems.md](./concepts/memory-systems.md)（含純短期 vs 長期向量記憶權衡）
  - [guardrails-and-evals.md](./concepts/guardrails-and-evals.md)（含 Prompt 約束 vs 程式碼硬護欄權衡）
- 編譯 2 篇架構模式頁面（patterns/）：
  - [react-pattern.md](./patterns/react-pattern.md)（Thought-Action-Observation 循環與三大失效防護）
  - [orchestration-patterns.md](./patterns/orchestration-patterns.md)（Single-Agent vs Multi-Agent Manager/Handoff 選型決策）
- 編寫 1 篇動手實作教學頁面（guides/）：
  - [minimal-agent-from-scratch.md](./guides/minimal-agent-from-scratch.md)（原生極簡 Python Zero-heavy-framework 實作）
- 同步更新總目錄 [index.md](./index.md)。
