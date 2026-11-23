# How to Build an AI Agent: From Scratch to Production (aiagent.org)

- **來源網址**: https://aiagent.org/tutorials/aiagent
- **主題分類**: 從零實作教學（Minimum Viable Agent）

## 核心重點摘要

1. **為什麼要從零手寫一個 Agent？**
   - 直接套用高階框架（如 LangChain、CrewAI）容易隱藏底層運作邏輯，導致除錯困難。用純 Python 從零實作能清楚掌握狀態管理、迴圈控制與失敗處理。
2. **最小可行 Agent（MVP）的四大基礎元件**
   - **Reasoning Engine（推理引擎）**：透過 System Prompt 賦予 LLM 目標與可用工具清單，讓模型輸出結構化的思考與下一步行動（例如 JSON 格式的 `tool_name` 與 `arguments`）。
   - **Tool Registry（工具註冊表）**：將 Python 函式包裝成標準化的工具描述（包含名稱、功能說明、參數型別 Schema），讓模型知道何時該呼叫哪支函式。
   - **Memory System（記憶系統）**：
     - *短期記憶（Short-term Memory）*：維護當前的訊息歷史陣列（`messages`），記錄每輪的提問、工具呼叫與回傳結果。
     - *長期記憶（Long-term Memory）*：透過向量資料庫或外部檔案儲存跨對話的使用者偏好與歷史經驗。
   - **Execution Loop（執行迴圈）**：控制「呼叫 LLM → 解析工具需求 → 執行本地 Python 函式 → 將結果塞回對話歷史 → 再次呼叫 LLM」的 `while` 迴圈，直到模型判斷任務完成並輸出最終答案。
3. **從原型邁向正式環境（Production）的關鍵步驟**
   - **強制結構化輸出（Structured Outputs）**：使用 JSON Schema 限制模型輸出格式，避免解析工具參數時發生格式錯誤。
   - **加入最大迭代上限（`max_iterations`）**：防止 Agent 在遇到無法解決的問題時陷入無限迴圈並耗盡 API 額度。
   - **可觀測性（Observability）**：記錄每一次迭代的輸入、思考過程、工具耗時與回傳值，方便事後追蹤錯誤節點。
