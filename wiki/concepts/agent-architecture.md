---
title: "Agent 解剖結構與核心元件 (Agent Architecture)"
type: concept
sources:
  - "../sources/01_openai_practical_guide.md"
  - "../sources/02_build_ai_agent_from_scratch.md"
  - "../sources/03_anatomy_of_an_ai_agent.md"
last_updated: "2026-11-23"
tags:
  - concept
  - architecture
  - anatomy
  - core-components
---

# Agent 解剖結構與核心元件 (Agent Architecture)

## 1. 什麼是 AI Agent？

在傳統軟體開發中，自動化流程主要依賴硬編碼的條件判斷（if-else）與靜態規則引擎；而 **AI Agent** 則是利用大型語言模型（LLM）作為核心推理引擎，自主管理工作流的執行過程。Agent 具備代表使用者做決策、調度外部工具，以及自行判斷任務何時達成並結束運作的能力。（來源：[OpenAI 實務指南](../sources/01_openai_practical_guide.md)）

若從計算本質來看，單純的 LLM 只是一個「無狀態的文字預測器」，缺乏對外部世界的感知與影響力。要讓 LLM 蛻變為具備主動解決問題能力的 Agent，必須以模型為核心，擴充周邊的控制結構與交互元件。（來源：[Agent 的解剖結構](../sources/03_anatomy_of_an_ai_agent.md)）

---

## 2. 核心元件拆解

整合實務與架構文獻，一個完整的 AI Agent 系統包含以下關鍵組成要素：

```mermaid
flowchart TD
    subgraph Brain [推理核心 Brain]
        Model[LLM 推理引擎]
        Inst[Instructions / System Prompt 角色與規範]
    end

    subgraph MemoryLayer [記憶系統 Memory]
        ShortMem[短期工作記憶 (Context Window / Messages)]
        LongMem[長期記憶 (語意與情景記憶)]
    end

    subgraph ActionPlanning [規劃與執行]
        Plan[Planning 任務拆解與反思]
        Exec[Execution Loop 執行迴圈]
    end

    subgraph Interaction [外圍互動]
        Tools[Tool Registry 工具註冊表]
        Safety[Guardrails 安全護欄]
    end

    Brain <--> MemoryLayer
    Brain <--> ActionPlanning
    ActionPlanning <--> Tools
    Safety -.-> Brain
    Safety -.-> Tools
```

### 2.1 推理核心（Reasoning Engine / Model）
* 模型是 Agent 的「大腦」，負責語意理解、多步驟推理、策略規劃與最終決策。
* 實務策略：建議初期以頂級前沿模型（如 GPT-4o、Claude 3.5 Sonnet）建立基準線（Baseline），確認整體推理鏈路通順後，再將特定子任務或工具解析委派給更輕量、快速的模型以節省延遲與成本。（來源：[OpenAI 實務指南](../sources/01_openai_practical_guide.md)）

### 2.2 指令與規範（Instructions / System Prompt）
* 定義 Agent 的角色設定、專業領域邊界、任務目標、工具呼叫準則以及在缺乏資料時的降級行為。清晰明確的 Instructions 能有效收斂模型輸出，避免幻覺與偏題。（來源：[OpenAI 實務指南](../sources/01_openai_practical_guide.md)）

### 2.3 工具體系（Tools & Tool Registry）
* 工具是 Agent 的「四肢」與「感官」，將模型與外部世界連結。
* 工具必須具備結構化的定義（包含函式名稱、功能說明與 JSON Schema 參數規格），涵蓋查詢（Data）、操作（Action）與編排（Orchestration）三種層級。（來源：[工具使用與註冊機制](./tool-use-and-registry.md)）

### 2.4 記憶體系（Memory System）
* **短期工作記憶（Working Memory）**：維護單次對話或任務執行時的上下文（Messages History）。
* **長期持久記憶（Long-term Memory）**：索引跨對話的經驗、使用者偏好或業務知識庫。（來源：[Agent 記憶體系](./memory-systems.md)）

### 2.5 執行迴圈（Execution Loop）
* 控制狀態轉移的驅動核心，通常由最純粹的 `while` 迴圈構成。負責持續推進「呼叫 LLM → 解析動作 → 執行本地工具 → 反饋寫回歷史」的循環，直到產出最終答案或達到終止條件。（來源：[從零實作教學](../sources/02_build_ai_agent_from_scratch.md)）

---

## 3. 何時該用 Agent？（適用邊界評估）

在導入 Agent 架構前，必須評估是否真的需要這種靈活性，避免「過度工程化」：

| 評估維度 | 適合使用 Agent 的情境 | 建議使用傳統腳本（Script/Rule）的情境 |
| :--- | :--- | :--- |
| **決策複雜度** | 邏輯模糊、條件分支難以窮舉（例如爭議退款、複雜合規判定） | 條件邏輯固定、路徑已知且單純（如「若超過 7 天則拒絕」） |
| **資料結構** | 涉及大量非結構化文字、郵件、跨多文檔比對與摘要 | 資料結構整齊且格式已知（如單純 SQL 欄位映射） |
| **交互多樣性** | 需要多輪與外部系統互動，並依據即時回饋動態調整下一步 | 步驟固定且線性的排程任務（如每日定時匯出 CSV） |
| **容錯與成本** | 可接受合理延遲與 API 呼叫成本，著重自動化能力 | 對毫秒級延遲與 100% 確定性有嚴格要求 |

（來源：[OpenAI 實務指南](../sources/01_openai_practical_guide.md)）

---

## 4. 觀點分歧與權衡（Trade-offs / Perspectives）

### 議題：極簡核心架構 vs 完備功能器官

在探討 Agent 解剖結構時，不同文獻對於「構成 Agent 的最低必要元件」提出了不同維度的視角：

1. **極簡實作派立場（以 `aiagent.org` 為代表）**
   * **核心觀點**：最小可行 Agent（MVP）僅需四個基礎：推理引擎、工具註冊表、對話歷程（短期記憶）與 `while` 迴圈。（來源：[從零實作教學](../sources/02_build_ai_agent_from_scratch.md)）
   * **優勢與適用場景**：程式碼完全透明可控（50～100 行即可成形），容易除錯與理解狀態變化；適合單一任務型工具代理、概念驗證（PoC）與輕量自動化。
2. **完備認知架構派立場（以 `Arun Seetharaman` 為代表）**
   * **核心觀點**：現代成熟的 Agent 必須具備「規劃（Planning）」、「反思（Self-reflection）」與「情景/語意長效記憶（Episodic Memory）」三大高階器官，否則只是單純的 Tool Caller，無法應付長程複雜目標。（來源：[Agent 的解剖結構](../sources/03_anatomy_of_an_ai_agent.md)）
   * **優勢與適用場景**：具備任務自我修復與經驗泛化能力；適合高自主性研究代理、個人長效助理或多步驟旅遊/專案規劃。
3. **架構選型權衡建議**：
   * **初期建構**：強烈建議先遵循「極簡實作派」架構，以原生 Python 建立 MVP，確保執行迴圈與工具呼叫穩健。
   * **進階擴充**：當業務出現「需要跨對話保持上下文」或「需要自主拆解十個以上子步驟」時，再漸進式引入 Planning 模組與向量長期記憶，切忌在初期過度堆疊抽象層。
