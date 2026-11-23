# The Anatomy of an AI Agent: Planning, Memory, and Tool Use

- **來源網址**: https://medium.com/@arunseetharaman/the-anatomy-of-an-ai-agent-planning-memory-and-tool-use-20c59985197c
- **主題分類**: 核心組成拆解

## 核心重點摘要

1. **LLM 本身只是「無狀態的文字預測器」**
   - 單純的 LLM 沒有持久記憶，也無法主動改變外部環境。要將 LLM 升級為 AI Agent，必須為它裝上三個核心器官：**規劃（Planning）**、**記憶（Memory）** 與 **工具使用（Tool Use）**。
2. **規劃能力（Planning）**
   - **任務拆解（Task Decomposition）**：面對模糊的大目標（例如「幫我規劃一趟三天兩夜的東京行程並訂好餐廳」），Agent 會先將其拆解為多個可執行的子任務序列。
   - **自我反思與修正（Self-Reflection）**：執行過程中若發現某個子任務失敗（如餐廳客滿），Agent 能重新評估計畫並調整後續步驟，而非直接報錯中止。
3. **記憶系統（Memory）**
   - **工作記憶（Working Memory / Context Window）**：存放當前任務的即時上下文與推理軌跡。
   - **情景記憶與語意記憶（Episodic & Semantic Memory）**：工具執行後產生的結果與經驗會被回填並索引至記憶系統中，讓每次成功或失敗的互動都成為後續決策的依據，實現跨時間的學習與連貫性。
4. **工具使用（Tool Use）**
   - 工具延伸了模型的能力邊界，使其能獲取即時資訊（搜尋引擎）、執行精確計算（Python 直譯器）或操作外部系統（API 呼叫）。
   - 三者形成閉環：**規劃決定要用什麼工具 → 工具產生新的觀察結果 → 結果寫入記憶並更新下一輪規劃**。
