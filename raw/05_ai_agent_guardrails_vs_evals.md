# AI Agent Guardrails vs Evals: Building Reliable Agents (Arize AI)

- **來源網址**: https://arize.com/blog/ai-agent-guardrails-vs-evals/
- **主題分類**: 護欄與可靠度設計

## 核心重點摘要

1. **Guardrails（護欄）與 Evals（評估）的核心差異**
   - **Guardrails（執行期防護網）**：在程式碼與系統層級設立的硬性規則，**即時攔截**不安全、越權或失控的行為，防止錯誤發生在最終使用者面前。
   - **Evals（離線／線上品質評估）**：透過測試資料集與評分標準，**量測** Agent 的任務完成率、忠實度與推理品質，用來指導系統迭代與優化。
2. **設計有效 Guardrails 的四大面向**
   - **輸入與範圍限制（Input & Scope Guardrails）**：過濾無關請求，確保 Agent 只在授權的知識邊界內回答問題，資料不足時誠實停下而非硬湊答案。
   - **工具權限與參數驗證（Tool Execution Guardrails）**：在真正執行工具前，用程式腳本檢查參數合法性（例如金額上限、唯讀與寫入權限隔離）。
   - **迴圈與重試剎車（Loop & Retry Limits）**：明確規定單一節點最多重試幾次（例如最多 2～3 輪），達到上限即觸發停止條件或降級處理。
   - **輸出格式與忠實度檢查（Output Guardrails）**：在交付成品前，透過自動化腳本檢查格式（如字數、出處標註）並由獨立審查機制確認內容未偏離原始資料。
3. **形成持續改進的閉環**
   - 將線上執行時觸發 Guardrails 攔截或失敗的案例收集起來，轉化為新的 Evals 測試案例，並把檢查規則往前移到更早的執行節點，從源頭降低錯誤率。
