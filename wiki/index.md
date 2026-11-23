# 知識庫總目錄 (Index)

歡迎來到「簡單實作 AI Agents」知識庫。本知識庫由 LLM 依據 Karpathy 的 LLM Wiki 模式建立與持續維護。所有知識皆由不可變的 `raw/` 原始文獻提煉合流而成，並以標準 Markdown 相對路徑組織。

---

## 📚 原始文獻筆記 (Sources)

* [01_openai_practical_guide.md](./sources/01_openai_practical_guide.md) - OpenAI 實務指南：Agent 核心定義、適用情境、三要素（Model/Tools/Instructions）與 Multi-Agent 編排模式。
* [02_build_ai_agent_from_scratch.md](./sources/02_build_ai_agent_from_scratch.md) - aiagent.org 實作教學：捨棄厚重框架，以純 Python 建構最小可行 Agent（MVP）的四大基石。
* [03_anatomy_of_an_ai_agent.md](./sources/03_anatomy_of_an_ai_agent.md) - 解剖 AI Agent：解析 LLM 文字預測本質，以及升級所需的 Planning、Memory 與 Tool Use 三大器官。
* [04_react_agent_pattern.md](./sources/04_react_agent_pattern.md) - Decagon ReAct 設計模式：Reasoning + Acting 交織循環機制、實務軌跡與三大失效防護（Tool Loops, Drift, Injection）。
* [05_ai_agent_guardrails_vs_evals.md](./sources/05_ai_agent_guardrails_vs_evals.md) - Arize 護欄與評估：執行期硬防護（Guardrails）與離線品質衡量（Evals）的職責邊界與改進閉環。

---

## 💡 核心概念與元件 (Concepts)

* [agent-architecture.md](./concepts/agent-architecture.md) - **Agent 解剖結構與核心元件**：定義 Agent 本質、核心器官拆解、何時該用 Agent 評估矩陣，以及極簡派 vs 認知架構派的權衡。
* [tool-use-and-registry.md](./concepts/tool-use-and-registry.md) - **工具使用與註冊機制**：Data / Action / Orchestration 三大工具分類、Tool Registry 架構與結構化輸出（Structured Outputs）規範。
* [memory-systems.md](./concepts/memory-systems.md) - **Agent 記憶體系**：短期工作記憶（Working Memory）與長期記憶（情景與語意記憶）的分層架構與上下文修剪策略。
* [guardrails-and-evals.md](./concepts/guardrails-and-evals.md) - **安全護欄與評測體系**：輸入、工具、迴圈、輸出四大護欄維度、Human-in-the-loop 人工安全閥與離線 Evals 閉環。

---

## 🔄 架構與設計模式 (Patterns)

* [react-pattern.md](./patterns/react-pattern.md) - **ReAct 推理與行動模式**：Thought → Action → Observation 閉環、典型 Log Trace、三大踩坑失效模式（Tool Loops / Drift / Prompt Injection）與防範策略。
* [orchestration-patterns.md](./patterns/orchestration-patterns.md) - **Agent 編排模式**：Single-Agent 模式、Manager 管理者模式與 Decentralized Handoff 交接模式之架構比較與選型決策樹。

---

## 🛠️ 動手實作指南 (Guides)

* [minimal-agent-from-scratch.md](./guides/minimal-agent-from-scratch.md) - **從零手寫極簡 Agent 指南**：Zero-heavy-framework 原生 Python 最小可行實作，完整示範 Tool Registry、`while loop`、訊息記憶與最大迭代次數煞車。

---

## 📌 快速檢索說明
* 依循 [AGENTS.md](../AGENTS.md) 所定義的規範，每次 Ingest、擴充頁面或沉澱問答時，Agent 將即時更新此目錄與關聯連結。
