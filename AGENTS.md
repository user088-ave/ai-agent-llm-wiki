# AGENTS.md - LLM Wiki 維護規範與架構指南

本文件為「簡單實作 AI Agents」知識庫的核心 Schema 與 Agent 維護規範。所有維護、檢索、擴充與審核此知識庫的 LLM 均須嚴格遵守本指南。

---

## 1. 核心理念與架構 (The Core Concept & Architecture)

本知識庫遵循 Andrej Karpathy 的 **LLM Wiki** 模式：知識不是在每次提問時重新檢索原始文件（RAG），而是透過 LLM 持續累積、提煉並維護一個結構化、互相關聯的持久化知識庫（Persistent Wiki）。

知識庫分為三層架構：
1. **原始文獻層（Raw sources - `raw/`）**：存放不可變的外部來源文件。此為真理來源（Source of Truth），LLM 僅能讀取，嚴禁修改或刪除。
2. **Wiki 知識層（The wiki - `wiki/`）**：由 LLM 擁有並維護的 Markdown 文件集合。包含來源精華、概念、架構模式、實作指南、索引與日誌。
3. **規範架構層（The schema - `AGENTS.md`）**：即本文件。定義 Wiki 的組織結構、命名規範、工作流與維護標準。

---

## 2. 目標讀者與寫作規範 (Audience & Writing Style)

* **目標讀者**：漸進式／全員通用（Progressive Disclosure）。
  * 概念篇：通俗平易，非工程師或初學者亦能理解 Agent 運作本質。
  * 實作篇：直觀純粹，提供最小可行實作（Minimal Viable Agent），便於動手驗證。
* **語言風格**：以正體中文（繁體中文）撰寫。
* **專有名詞策略**：通用技術專有名詞保留英文原文（例如 `Prompt`、`Guardrails`、`Tool Calling`、`ReAct loop`、`Evals`、`Memory`、`Planning`、`Orchestration`），避免生硬刻板的翻譯。
* **程式碼實作哲學**：**原生極簡 Python（Zero-heavy-framework）**。絕不依賴 LangChain、LlamaIndex、CrewAI 等厚重框架。直接使用原生 Python 揭露 `while loop`、提示詞組裝、工具呼叫與訊息歷程（Message History）維護。

---

## 3. 目錄結構與頁面類型 (Taxonomy & Folder Structure)

```text
.
├── AGENTS.md                  # 本規範文件
├── raw/                       # 原始文獻（唯讀，Source of Truth）
│   ├── 01_openai_practical_guide_to_building_ai_agents.md
│   ├── 02_build_ai_agent_from_scratch.md
│   ├── 03_anatomy_of_an_ai_agent.md
│   ├── 04_react_agent_pattern.md
│   └── 05_ai_agent_guardrails_vs_evals.md
└── wiki/                      # 知識庫本體（LLM 維護）
    ├── sources/               # 原始文獻提煉頁
    ├── concepts/              # 核心概念與組成元件頁
    ├── patterns/              # 架構與設計模式頁
    ├── guides/                # 動手實作教學頁
    ├── index.md               # 內容導覽總目錄（Content-oriented）
    └── log.md                 # 異動與維護日誌（Chronological）
```

### 頁面類型定義：
1. **`wiki/sources/`（文獻提煉頁）**：
   * 每篇 raw 文件對應一篇獨立筆記。
   * 包含：原文元資料、核心論點摘要、關鍵架構圖（Mermaid）、雙向延伸概念列表。
2. **`wiki/concepts/`（核心概念頁）**：
   * 聚焦單一核心觀念（如 Agent 解剖結構、Tool Calling 機制、Memory 記憶體系、Guardrails 護欄、Evals 評測）。
   * 整合多篇文獻的觀點與定義。
3. **`wiki/patterns/`（架構與設計模式頁）**：
   * 探討 Agent 的思考與行動迴圈（如 ReAct Pattern、Plan-and-Solve、Routing、Reflection）。
4. **`wiki/guides/`（動手實作教學頁）**：
   * 提供從零構建 Agent 的步驟解說與可執行的極簡原生 Python 程式碼。
5. **`wiki/index.md`（目錄導覽）**：
   * 依分類列出所有 Wiki 頁面連結與一句話摘要，方便快速檢索與跳轉。
6. **`wiki/log.md`（歷程日誌）**：
   * 追加式（Append-only）紀錄每次 Ingest、Query 沉澱或 Lint 動作。

---

## 4. 格式與連結規範 (Formatting & Linking)

### 4.1 超連結規範
* 一律採用**標準 Markdown 相對路徑連結**，確保跨平台相容性（相容 GitHub、VS Code、標準閱讀器與 Obsidian）：
  * 從概念頁連到其他概念：`[Tool Calling](./tool-calling.md)`
  * 從指南頁連到概念頁：`[ReAct Pattern](../patterns/react-pattern.md)`
  * 從概念頁連到文獻來源：`[OpenAI 實務指南](../sources/01_openai_guide.md)`

### 4.2 頁首 Frontmatter 規範
所有 Wiki 頁面開頭必須包含輕量 YAML Frontmatter：

```markdown
---
title: "頁面標題"
type: concept | pattern | guide | source
sources:
  - "../sources/01_openai_guide.md"
last_updated: "2026-11-23"
tags:
  - agent
  - react
---
```

### 4.3 出處標註（Provenance）規範
* **論點級精準標註**：內文中任何引用自文獻的主張、原則或架構論點，必須在該段落或列表後標註來源，例：
  > 現代 Agent 本質上是一個包含 LLM 推理、工具執行與環境回饋的循環控制系統。（來源：`[Agent 的解剖結構](../sources/03_anatomy_of_an_ai_agent.md)`）
* **雙向追蹤**：在 `sources/` 頁面末端，必須設立 `## 貢獻與延伸概念`，明確列出由該篇文獻衍生出或更新的 `concepts/`、`patterns/` 與 `guides/` 頁面連結。

### 4.4 觀點分歧處理（Trade-offs / Perspectives）規範
當不同文獻出現觀點衝突（例如：何時該用原生寫法 vs 框架、是否需要複雜 Planning、Guardrails 與 Evals 的職責邊界）時：
* 嚴禁擅自簡化或二選一。
* 必須在該概念或模式頁設立 `## 觀點分歧與權衡（Trade-offs / Perspectives）` 章節。
* 具體列出：不同文獻的主張、適用情境、潛在成本與推薦取捨考量。

---

## 5. Agent 核心工作流 (Standard Workflows)

### 5.1 Ingest 工作流（文獻攝取）
當攝取新的 raw 來源或批次處理現存文獻時，Agent 須執行以下標準程序：
1. **讀取原始文件**：研讀 `raw/` 中的指定來源，萃取核心價值與實作細節。
2. **建立/更新 `wiki/sources/<name>.md`**：撰寫該文獻的提煉摘要、核心圖表與延伸索引。
3. **編譯/更新 Wiki 頁面**：
   * 將文獻內容合流至對應的 `concepts/`、`patterns/` 或 `guides/`。
   * 若有新概念則建立新頁；若屬既有主題則更新現有頁面，強化跨文獻整合。
   * 標註出處相對連結；若有相異觀點，補入「觀點分歧與權衡」章節。
4. **更新 `wiki/index.md`**：將新建立或更新的頁面納入總目錄，並維持單行摘要最新。
5. **記錄至 `wiki/log.md`**：追加一行標準格式日誌：
   `## [YYYY-MM-DD] ingest | <來源名稱或檔案名稱> - <一句話摘要說明>`

### 5.2 Query 工作流（問題回答與知識沉澱）
1. **優先檢索 Wiki**：先讀取 `wiki/index.md` 找到相關頁面，再深入研讀 `concepts/`、`patterns/` 與 `guides/`。
2. **綜合回答**：提供結構清晰、附帶出處相對連結的回答。
3. **沉澱回流（Compounding Artifacts）**：若使用者的提問引導出深刻的架構比較、實作指南或新視角，Agent 應主動建議或直接將精華整理回流為 Wiki 的新頁面或擴充既有頁面，並同步更新 `index.md` 與 `log.md`。

### 5.3 Lint 工作流（健康檢查六大標準）
定期或於維護階段執行 Wiki 健康度檢查：
1. **孤島檢查（Orphan check）**：確認沒有任何頁面遺漏在 `wiki/index.md` 之外，或缺乏任何入站相對連結。
2. **失效連結（Broken links）**：驗證所有 Markdown 相對路徑均能正確解析至實際檔案。
3. **未定義概念（Missing concepts）**：檢查各頁面中是否反覆出現未獨立成頁的關鍵核心術語。
4. **出處覆蓋（Provenance check）**：驗證核心論點是否皆具備 `../sources/` 溯源標註。
5. **矛盾檢驗（Contradiction check）**：確保跨頁面的觀點分歧皆已妥善收錄在「觀點分歧與權衡」章節。
6. **目錄與日誌同步（Index/Log integrity）**：確保所有頁面皆收錄於 `index.md`，且所有攝取／結構異動皆記錄於 `log.md`。

---

## 6. 日誌格式規範 (`wiki/log.md`)

每筆日誌開頭採用一致的格式，以利腳本或命令列解析：
```markdown
## [YYYY-MM-DD] <操作類型> | <標題或說明>
- 詳細項目 1
- 詳細項目 2
```
* 操作類型包括：`init`（初始化）、`ingest`（文獻攝取）、`update`（頁面修訂）、`query-archive`（問答沉澱）、`lint`（健康檢查）。
