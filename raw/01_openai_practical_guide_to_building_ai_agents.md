# A Practical Guide to Building AI Agents (OpenAI)

- **來源網址**: https://openai.com/business/guides-and-resources/a-practical-guide-to-building-ai-agents/
- **主題分類**: 實作總覽與架構指南

## 核心重點摘要

1. **什麼是 AI Agent？**
   - 傳統軟體是依照寫死的規則（if-else）自動化流程；AI Agent 則是利用大型語言模型（LLM）自主管理工作流程的執行，具備獨立代表使用者做決策、呼叫外部工具與控制流程結束時機的能力。
2. **什麼時候該用 AI Agent？（適用情境）**
   - **複雜且模糊的決策**：例如客戶退款審核、風險評估，無法用單純規則窮舉。
   - **難以維護的規則系統**：傳統規則引擎過於龐大且脆弱（如合規性審查）。
   - **高度依賴非結構化資料**：需要同時處理自然語言、PDF 文件、對話紀錄與多輪互動。
   - *若任務流程完全固定且可用簡單腳本完成，則不需要使用 Agent。*
3. **AI Agent 的三大核心基礎元件**
   - **Model（模型）**：負責推理與決策的大腦。實務上建議先用最強的模型建立基準線（Baseline），驗證流程可行後，再針對簡單子任務替換為較小、較快、較省成本的模型。
   - **Tools（工具）**：讓 Agent 與外部世界互動的介面，分為三類：
     1. *資料查詢類（Data）*：讀取資料庫、搜尋知識庫、查詢 API。
     2. *動作執行類（Action）*：寄送 Email、更新 CRM、修改訂單狀態。
     3. *編排類（Orchestration）*：將另一個專職 Agent 當作工具呼叫。
   - **Instructions（指令規範）**：明確定義 Agent 的角色、任務邊界、工具使用時機與異常處理規則，減少模型猜測。
4. **編排模式（Orchestration Patterns）**
   - **單一 Agent 系統（Single-Agent）**：由一個模型搭配一組工具循環執行，直到達成目標或觸發停止條件。建議從單一 Agent 開始實作，避免過早複雜化。
   - **多 Agent 系統（Multi-Agent）**：當工具數量過多導致選擇混淆，或指令過於龐雜時，可拆分為「管理者模式（Manager Pattern，由中央 Agent 分配任務）」或「去中心化模式（Decentralized Pattern，Agent 之間直接交接 Handoff）」。
5. **安全護欄（Guardrails）與人工介入（Human-in-the-loop）**
   - 必須設置多層防護：相關性過濾（Relevance）、安全分類器、PII 個資遮罩、工具風險分級。
   - 遇到「超過最大重試次數」或「高風險操作（如大額退款、刪除資料）」時，必須暫停並轉交人工審核。
