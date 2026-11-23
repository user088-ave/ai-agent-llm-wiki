---
title: "從零手寫極簡 Agent 指南 (Minimal Agent from Scratch)"
type: guide
sources:
  - "../sources/01_openai_practical_guide.md"
  - "../sources/02_build_ai_agent_from_scratch.md"
  - "../sources/04_react_agent_pattern.md"
  - "../sources/05_ai_agent_guardrails_vs_evals.md"
last_updated: "2026-11-23"
tags:
  - guide
  - python
  - tutorial
  - implementation
  - zero-framework
  - mvp
---

# 從零手寫極簡 Agent 指南 (Minimal Agent from Scratch)

本指南展示如何**完全不依賴任何重型框架**（如 LangChain、LlamaIndex 或 CrewAI），僅使用原生 Python 與標準 LLM API，從零打造一個具備思考、工具呼叫、狀態記憶與安全煞車的最小可行 Agent（Minimum Viable Agent）。（來源：[從零實作教學](../sources/02_build_ai_agent_from_scratch.md)）

---

## 1. 核心設計架構

我們要實作的極簡 Agent 由四大核心積木組成：
1. **Tool Registry（工具註冊表）**：定義真實的 Python 函式及其對應的 JSON Schema。
2. **Short-term Memory（短期記憶）**：用純 Python List 維護對話訊息歷程（`messages`）。
3. **Execution Loop（執行迴圈）**：由 `while loop` 驅動，實現 ReAct 思考與行動循環。
4. **Safety Guardrails（安全護欄）**：硬編碼的最大迭代次數（`max_iterations`）與異常攔截。

---

## 2. 步驟拆解與程式碼實作

### 步驟 1：定義本地工具與工具註冊表（Tools & Registry）

首先定義真實執行的 Python 函式，並建立向 LLM 描述工具介面的 Schema：

```python
import json

# 1. 本地實體函式 (Callable Functions)
def get_weather(city: str) -> str:
    """查詢指定城市天氣"""
    fake_db = {
        "台北": "陰天有短暫雨，氣溫 22°C",
        "東京": "晴天，氣溫 16°C",
        "紐約": "多雲，氣溫 10°C"
    }
    return fake_db.get(city, f"找不到城市【{city}】的天氣資訊。")

def calculate(expression: str) -> str:
    """安全的數學運算計算器"""
    try:
        # 僅允許基礎數學字元，防止任意程式碼注入
        allowed = set("0123456789+-*/(). ")
        if not all(c in allowed for c in expression):
            return "錯誤：運算式包含不安全字元"
        return str(eval(expression, {"__builtins__": None}, {}))
    except Exception as e:
        return f"計算錯誤: {str(e)}"

# 2. 工具註冊表映射
TOOL_REGISTRY = {
    "get_weather": get_weather,
    "calculate": calculate
}

# 3. 提供給 LLM 的標準 Tool Calling Schema
TOOLS_SCHEMA = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "查詢指定城市的即時天氣狀況與氣溫",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {"type": "string", "description": "城市名稱，例如：台北、東京"}
                },
                "required": ["city"]
            }
        }
    },
    {
        "type": "function",
        "function": {
            "name": "calculate",
            "description": "計算數學運算式之數值結果",
            "parameters": {
                "type": "object",
                "properties": {
                    "expression": {"type": "string", "description": "數學運算式，例如：(25 * 4) + 10"}
                },
                "required": ["expression"]
            }
        }
    }
]
```

（來源：[工具使用與註冊機制](../concepts/tool-use-and-registry.md)）

---

### 步驟 2：核心執行迴圈（Execution While Loop）

這是 Agent 最純粹的「大腦與神經中樞」，完整揭露了狀態管理與 ReAct 循環：

```python
from openai import OpenAI

def run_minimal_agent(user_prompt: str, max_iterations: int = 5) -> str:
    """
    原生極簡 Agent 執行核心
    :param user_prompt: 使用者提問
    :param max_iterations: 最大防護迭代次數（Guardrail）
    """
    client = OpenAI() # 讀取環境變數 OPENAI_API_KEY
    
    # 初始化短期工作記憶 (Working Memory)
    messages = [
        {
            "role": "system",
            "content": (
                "你是一個可靠的 AI 助理。請仔細分析使用者問題，並適時使用工具完成任務。"
                "若已經取得完整答案，請直接輸出最終回答。"
            )
        },
        {"role": "user", "content": user_prompt}
    ]
    
    iteration = 0
    print(f"\n[Agent 啟動] 目標：{user_prompt}")

    # ReAct 核心執行迴圈
    while iteration < max_iterations:
        iteration += 1
        print(f"\n--- 第 {iteration} 輪迭代 ---")

        # 呼叫推理引擎 (Reasoning Engine)
        response = client.chat.completions.create(
            model="gpt-4o",
            messages=messages,
            tools=TOOLS_SCHEMA,
            tool_choice="auto"
        )
        
        response_message = response.choices[0].message
        messages.append(response_message) # 記錄推理歷史

        # 檢查是否需要執行工具 (Action)
        tool_calls = response_message.tool_calls
        
        if not tool_calls:
            # 模型未呼叫工具，代表已推導出 Final Answer
            print("[Agent 完成] 產出最終回覆。")
            return response_message.content

        # 執行外部工具調用
        for tool_call in tool_calls:
            func_name = tool_call.function.name
            args = json.loads(tool_call.function.arguments)
            call_id = tool_call.id
            
            print(f"[Action] 呼叫工具: {func_name}({args})")
            
            # 工具執行護欄：檢查函式是否存在
            if func_name in TOOL_REGISTRY:
                try:
                    tool_result = TOOL_REGISTRY[func_name](**args)
                except Exception as err:
                    tool_result = f"執行錯誤: {str(err)}"
            else:
                tool_result = f"錯誤：未定義的工具名稱 {func_name}"

            print(f"[Observation] 工具回傳: {tool_result}")

            # 將觀察結果（Observation）作為 tool 角色寫回記憶
            messages.append({
                "role": "tool",
                "tool_call_id": call_id,
                "name": func_name,
                "content": str(tool_result)
            })

    # 觸發最大次數護欄煞車
    print(f"[Guardrail 觸發] 超過最大迭代次數 {max_iterations}，強制終止。")
    return "抱歉，我在此任務的多輪嘗試中無法取得滿意的結論，請稍後重試或精準化您的提問。"
```

（來源：[從零實作教學](../sources/02_build_ai_agent_from_scratch.md)、[ReAct 推理與行動模式](../patterns/react-pattern.md)）

---

## 3. 測試與執行驗證

撰寫簡單測試程式碼驗證多步驟推論與多工具串接：

```python
if __name__ == "__main__":
    # 測試情境：需要同時查詢天氣並執行數學運算的複合問題
    test_query = "台北跟東京的天氣如何？如果台北氣溫加上東京氣溫再乘以 2，數值是多少？"
    
    final_output = run_minimal_agent(test_query)
    print("\n========== 最終成果 ==========")
    print(final_output)
```

### 預期執行日誌軌跡（Log Trace）：
1. **第 1 輪**：LLM 思考後同時產出兩個 Action：`get_weather(city="台北")` 與 `get_weather(city="東京")`。
2. **觀察**：系統執行兩支工具，回傳台北 22°C 與東京 16°C。
3. **第 2 輪**：LLM 接收到資料，產出 Action：`calculate(expression="(22 + 16) * 2")`。
4. **觀察**：系統計算出 `76`。
5. **第 3 輪**：LLM 判定資訊充足，不再呼叫工具，輸出自然語言 Final Answer。

---

## 4. 生產化加固檢查清單（Production Checklist）

若要將此最小原型升級為企業上線等級，請對照以下規範加固：
- [ ] **參數校驗**：使用 `pydantic` 嚴格約束工具參數結構與數值範圍。（來源：[工具使用與註冊機制](../concepts/tool-use-and-registry.md)）
- [ ] **超時煞車（Timeout）**：為每一個工具呼叫設定執行 timeout，防止網路阻塞卡死。（來源：[安全護欄與評測體系](../concepts/guardrails-and-evals.md)）
- [ ] **敏感資訊遮罩（PII Masking）**：在將文字塞入 `messages` 前過濾電話、金流密碼與個人隱私資料。
- [ ] **可觀測性（Tracing）**：以日誌紀錄每輪對話的 Token 消耗、工具延遲與模型思考軌跡。
