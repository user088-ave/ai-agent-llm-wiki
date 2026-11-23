---
title: "文獻提煉：A Practical Guide to Building AI Agents (OpenAI)"
type: source
raw_source: "../../raw/01_openai_practical_guide_to_building_ai_agents.md"
last_updated: "2026-11-23"
tags:
  - source
  - openai
  - architecture
  - orchestration
  - guardrails
---

# A Practical Guide to Building AI Agents (OpenAI)

- **原始連結**：https://openai.com/business/guides-and-resources/a-practical-guide-to-building-ai-agents/
- **主題分類**：實作總覽與架構指南
- **對應原始檔**：`raw/01_openai_practical_guide_to_building_ai_agents.md`

---

## 核心論點摘要

1. **AI Agent 的定義與本質**
   * 傳統自動化仰賴寫死的規則（if-else）；AI Agent 則利用大型語言模型（LLM）自主管理工作流，具備代表使用者決策、呼叫外部工具並決定任務結束點的能力。
2. **適用情境（何時該用 Agent）**
   * 適合：模糊複雜決策（如退款審核）、維護成本過高的龐大規則系統（合規審查）、高度依賴非結構化多輪資料處理。
   * 不適合：流程完全固定、邏輯已知且可用一般腳本穩定完成的任務。
3. **三大核心基礎元件**
   * **Model（模型）**：負責決策的大腦。建議先用最強模型建立 Baseline，驗證可行後再針對子任務替換輕量經濟模型。
   * **Tools（工具）**：分為三大類別——資料查詢類（Data）、動作執行類（Action）與編排類（Orchestration，即將專職 Agent 當工具呼叫）。
   * **Instructions（指令規範）**：定義 Agent 角色、職責邊界、工具呼叫時機與異常處理規則。
4. **編排模式（Orchestration Patterns）**
   * **Single-Agent**：單模型搭配工具循環執行，是最佳實踐的起點，避免過早引入複雜度。
   * **Multi-Agent**：當單一 Agent 的工具過多或指令混亂時拆分，分為管理者模式（Manager Pattern）與去中心化交接模式（Decentralized / Handoff Pattern）。
5. **安全護欄（Guardrails）與人機協作（Human-in-the-loop）**
   * 包含相關性過濾、安全分類器、PII 遮罩、工具風險分級。
   * 遇到高風險操作（如大額金流、刪除資料）或超過重試上限時，必須交由人工介入。

---

## 核心架構圖

```mermaid
flowchart TD
    User([使用者請求]) --> GuardrailsIn[輸入護欄 / 安全過濾]
    GuardrailsIn --> Agent[Agent 核心 (Model + Instructions)]
    Agent --> Decision{決策分支}
    Decision -->|需要外部資訊/動作| ToolCall[呼叫 Tools]
    ToolCall -->|Data / Action / Orchestration| Env[(外部環境 / API / DB)]
    Env -->|回傳結果 Observation| Agent
    Decision -->|高風險操作 / 超出重試上限| HITL[Human-in-the-loop 人工審核]
    Decision -->|任務完成| GuardrailsOut[輸出護欄]
    GuardrailsOut --> Output([最終回覆])
```

---

## 貢獻與延伸概念

本篇文獻貢獻了以下 Wiki 頁面之核心論點：
* **[Agent 解剖結構與核心元件](../concepts/agent-architecture.md)**：定義了 Model、Tools、Instructions 三位一體元件。
* **[工具使用與註冊機制](../concepts/tool-use-and-registry.md)**：工具三大類別（Data, Action, Orchestration）分類標準。
* **[安全護欄與評測體系](../concepts/guardrails-and-evals.md)**：輸入過濾、工具風險分級與 Human-in-the-loop 機制。
* **[Agent 編排模式](../patterns/orchestration-patterns.md)**：Single-Agent 與 Multi-Agent（Manager vs Handoff）模式及選型決策。
