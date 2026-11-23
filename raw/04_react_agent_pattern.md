# What is the ReAct Agent Pattern? Reasoning and Acting in LLMs (Decagon)

- **來源網址**: https://decagon.ai/glossary/what-is-react-agent-pattern
- **主題分類**: 推理與行動迴圈（ReAct 設計模式）

## 核心重點摘要

1. **什麼是 ReAct 模式？**
   - ReAct 代表 **Reasoning（推理） + Acting（行動）**，是一種讓 LLM 交替產生「內部推理軌跡」與「外部工具動作」的經典 Agent 設計模式。
2. **ReAct 的標準三步驟循環**
   - **Thought（思考）**：模型分析目前已知資訊，推論接下來需要取得什麼資料或執行什麼動作。
   - **Action（行動）**：根據思考結果，選擇並呼叫對應的外部工具與參數（例如 `get_order_status(order_id="12345")`）。
   - **Observation（觀察）**：接收工具回傳的真實資料，將其納入上下文，接著進入下一輪的 `Thought`，直到收集足夠資訊並輸出 `Final Answer`。
3. **實務軌跡範例（查詢訂單狀態）**
   - *使用者提問*：「我的訂單 #12345 什麼時候會到？」
   - *Thought 1*：我需要先查詢訂單 #12345 的物流狀態。
   - *Action 1*：`check_shipping(order_id="12345")`
   - *Observation 1*：狀態為「已出貨」，預計送達日為 11 月 25 日。
   - *Thought 2*：我已經取得確切送達日期，可以回覆使用者了。
   - *Final Answer*：「您的訂單 #12345 已經出貨，預計將於 11 月 25 日送達。」
4. **實作 ReAct 時常見的三大踩坑（Failure Modes）**
   - **工具迴圈（Tool Loops）**：當工具回傳空值或錯誤訊息時，Agent 反覆用相同參數呼叫同一支工具。
   - **推理漂移（Reasoning Drift）**：多輪對話後上下文過長，模型忘記最初的目標或開始編造未經工具證實的資訊。
   - **間接提示詞注入（Prompt Injection）**：外部工具抓回來的網頁或文件中藏有惡意指令，劫持了 Agent 的下一步行動。
   - *解法*：設定最大步數限制（Step Limit）、對工具輸出做格式清理，並在關鍵步驟加入驗證檢查。
