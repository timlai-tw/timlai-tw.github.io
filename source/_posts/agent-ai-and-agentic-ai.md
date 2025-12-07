---
title: Agent AI 與 Agentic AI 的差別
date: 2025-12-06 09:22:29
tags:
 - Agent AI
 - Agentic AI
---

<img src="/post/2025/12/agent-ai-and-agentic-ai/agentic-ai.jpg" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/>

今年大家應該蠻常聽到 **Agent AI** 和 **Agentic AI** 這兩個詞，名稱看起來很像、都有「Agent」，但實際上指的東西不太一樣：一個是「單一代理」的能力，另一個則是「多代理系統」的整體架構。本篇文章以比較白話的比喻來整理兩者的差異，以及 Microsoft 在 Agentic AI 上目前提供的幾種主要解決方案：**Copilot Studio / Azure AI Foundry Agent Service / Microsoft Agent Framework / Semantic Kernel**，方便大家選擇適合自己的開發路線。

<!-- more -->

<br>

## 什麼是 AI Agent？什麼是 Agentic AI？

先用一句話來區分：

- **AI Agent** 比較像是一位「可以自己想辦法完成任務的助手」。
- **Agentic AI** 則是一個「由很多專業助手組成的團隊」，可以協調分工、一起完成一個複雜目標。

<br>
{% asset_img AI-Agent-vs-Agentic-AI.jpg %}
<br>

### AI Agent：會自己用工具的智慧助手

一般我們說的 **AI Agent（AI 代理）**，指的是一個以 LLM 為核心、能夠：

- 讀懂使用者的目標。
- 規劃完成任務需要的步驟。
- 在過程中主動呼叫工具。
- 根據工具回傳結果調整接下來要做什麼。

也就是說，它不再只是單純回覆文字，而是會「自己動手做事」，例如：

- 我們請它「幫我彙整一份這週專案進度並寄信給主管」，它會：
	1. 先去查專案系統的狀態（例如：Azure DevOps MCP）。
	2. 整理成文字報告。
	3. 呼叫工具寄信送出（例如：API 或 Microsoft Team Webhook)。

這整個流程都是由一個 Agent 自己掌握、自己決定何時呼叫哪一個工具。

### Agentic AI：多代理協作的系統

**Agentic AI（代理式 AI）** 則是把「多個 Agent」組織成一個協作系統，讓它們可以彼此溝通、分工合作，來處理一個單一 Agent 很難搞定的複雜任務。例如：

- 從一段 1 小時的 YouTube 影片，自動產生：逐字稿整理、重點大綱、教學文章、社群貼文。

在 Agentic AI 的架構下，我們可能會看到這樣的分工：

- **影片分析 Agent**：負責從影片拿到逐字稿、拆段、標註重點。
- **內容整理 Agent**：把資料整理成易讀的大綱、章節結構。
- **寫作 Agent**：依照不同輸出格式（部落格、教學、社群貼文）產生內容。
- **審稿 / 品質控管 Agent**：檢查語氣、品牌一致性、避免重複內容。

中間會有一個「協調者」負責安排工作流程、把上一個 Agent 的輸出當成下一個 Agent 的輸入，這就是所謂的 **Agentic workflow**。

### 差異重點整理

用表格簡單整理一下兩者差異：

| 特性 | AI Agent | Agentic AI |
| :-- | :-- | :-- |
| 任務範圍 | 一次專注在一個明確任務 | 處理跨多步驟、多角色的大目標 |
| 架構組成 | 以單一 LLM + 工具為主 | 多個 Agent + 協調流程 |
| 互動模式 | 使用者 → 單一 Agent → 工具 | 使用者 → 協調層 → 多個 Agent 協作 |
| 自主程度 | 中等：可以自己決定如何用工具 | 較高：可以自己規劃、分派、調整流程 |
| 資訊流向 | Input → Agent → Tools → Output | Input → Agent1 → Agent2 → … → Output |

如果把 AI Agent 比喻成一位「超強助理」，那 Agentic AI 就比較像是「一個專案團隊」，裡面有 PM、工程師、設計、測試，各司其職又互相協作。

<br>

## Microsoft 在 Agentic AI 上的解決方案版圖

談完概念之後，回到實務選型：**如果我想在 Microsoft 生態系裡做 Agent / Agentic AI，手上有哪些工具可以選？**

目前大致可以分成四條路線，從「幾乎不用寫程式」到「完全程式碼主導」都有：

| 解決方案 | 定位 | 適用對象 | 程式碼需求 |
| :-- | :-- | :-- | :-- |
| **Copilot Studio** | 低程式碼 AI Agent 平台 | 業務部門、一般開發者 | 幾乎不用寫程式 |
| **Azure AI Foundry Agent Service** | 雲端託管 Agent 服務 | 企業 IT / 後端工程師 | 中等程式碼量 |
| **Microsoft Agent Framework** | 開源 Agent / 多代理框架 SDK | 進階開發者、架構師 | 高度程式碼客製 |
| **Semantic Kernel** | 輕量級協調 SDK | .NET / Python 開發者 | 中高程式碼量 |

<br>
{% asset_img Microsoft-Agentic-AI-Comparison.jpg %}
<br>

### Copilot Studio：從「聊天機器人」長大的 Agent 平台

**Copilot Studio** 可以把它想成是「做聊天機器人、做工作流程型 Copilot」的低程式碼平台，很多情境其實已經很接近單一 Agent：

- 透過圖形化介面定義對話流程、條件分支。
- 連接各種資料來源（SharePoint、OneDrive、Dynamics、外部 API…）。
- 透過 **Connector / Plug‑in / MCP** 把外部系統當成工具來使用。
- 不需要自己處理 Prompt、記憶體、狀態儲存等細節。

**優點**：

- 幾乎不用寫程式，適合業務單位或一般開發者。
- 內建大量連接器，串接常見 SaaS / Microsoft 365 生態非常方便。
- 可以快速發佈到 Teams、Web、LINE 等多種通路。

**限制 / 缺點**：

- 複雜的 Agentic workflow（多代理協作、細緻的狀態機）比較難完全用圖形化介面表達。
- 對於版本控管、CI/CD、程式碼共用等軟體工程議題，彈性不如純程式碼框架。

如果你的需求是：「想要先做一個可以用的智慧助理，重點是快、好維護」，那 Copilot Studio 是個蠻好的起點。

<br>

### Azure AI Foundry Agent Service：雲端託管的 Agent 平台

**Azure AI Foundry Agent Service** 可以視為「由雲端幫你托管 Agent」，我們透過 SDK 或 Portal 定義好下列項目：

- 使用哪個模型（例如 Azure OpenAI GPT‑4o 或其他大語言模型）。
- 這個 Agent 可以用哪些工具（Function / API / 檔案 / 搜尋等）。
- 一些系統層的參數（如安全性、網路、儲存體、Log 等）。

優點是：

- **雲端全託管**：不需要自己維運負責推理的工作節點、負載平衡、監控、Log 儲存。
- **企業級安全性**：支援 VNet、Private Endpoint、BYO Storage 等企業需求。
- **多模型支援**：可以選用不同家族的模型（例如：GPT、Llama、GROK-4）依場景搭配。

相對的：

- 必須部署在 Azure，成本與規模需要事前評估。
- 仍然需要一定程度的程式碼與架構設計。

如果已經有開發團隊，希望把 Agent 當成一種「雲端服務元件」，又不想自己維護整套推理基礎架構，那 Azure AI Foundry Agent Service 會是一個折衷但穩健的選擇。

<br>

### Microsoft Agent Framework：多代理與 A2A 的核心 SDK

**Microsoft Agent Framework** 是最近 (2025/10/1) 才開源的 **Agent / Agentic AI 專用 SDK**，同時支援 **Python** 和 **.NET**，可以視為把原本的 **Semantic Kernel** 與 **AutoGen** 整併、提升後的新一代框架。

它的設計大致有下列幾項重點：

- **多代理協調**：原生支援 Agent 之間互相對話、協作，適合實作複雜工作流程。
- **A2A（Agent‑to‑Agent）協定**：讓不同執行環境、甚至不同平台上的 Agent 也能互通。
- **MCP / OpenAPI 工具整合**：可以很方便地把 API、外部系統包裝成 Agent 的工具。
- **生產環境就緒**：有錯誤處理、重試、持久化與觀測性等機制，方便從 PoC 走到 Production。

**優點**：

- 架構上就是為「Agentic AI」設計，很適合把多個專業 Agent 組合成工作流程。
- 與 Azure AI Foundry、Microsoft 365 Copilot、Copilot Studio 等平台都可以互相整合。
- 開源、可行自架設，也可以搭配雲端服務一起使用。

**缺點 / 挑戰**：

- 技術門檻相對高，需要熟悉 LLM + 分散式系統 + 軟體架構設計。
- 功能很多，設計彈性也高，前期會需要花時間建立團隊內部的 best practices。

從定位來看，它比較像是：

> 「如果你想打造一個可以長期演進、支援多代理協作的 AI 應用平台，Agent Framework 就是那個核心 SDK。」

<br>

### Semantic Kernel：輕量、模組化的協調 SDK

**Semantic Kernel** 則是比較早期推出的 LLM 協調 SDK，一樣支援 .NET / Python，主要幫我們處理：

- Prompt 組合與模板化。
- 指令 Function、工具呼叫（例如：把 C# method 變成可被模型呼叫的 function）。
- 記憶體 (Memory)、Planner 等常見 Agent 開發場景。

它本身也可以用來做「單一 Agent」、或是簡單的多 Agent 情境，不過 **多代理協作這一塊，目前的主角已經漸漸交棒給 Microsoft Agent Framework**，Semantic Kernel 則偏向：

- 在既有 .NET / Python 專案裡「嵌入 LLM 能力」的工具箱。
- 作為 Agent Framework 的一個重要組件或輔助 SDK。

**優點**：

- 輕量、好嵌入，對 .NET 開發環境相當友善。
- 社群成熟、文件與範例多，很多既有教學都以它為基礎。

**限制**：

- 如果要實作複雜的多代理 workflow，會需要自己多寫一層協調邏輯，或是搭配 Microsoft Agent Framework。

<br>

## 四種方案的比較與選擇建議

最後用一張對照表，看一下四種方案的差異：

| 特性 | Copilot Studio | Azure AI Foundry Agent Service | Microsoft Agent Framework | Semantic Kernel | 
| :-- | :-- | :-- | :-- | :-- |
| 開發方式 | 圖形化 / 對話式 | SDK + Portal 管理 | Python / .NET SDK | .NET / Python SDK |  
| 多代理協作 | 有限（偏向單一 Agent） | 支援，但由服務端協調 | 原生支援、多代理主場 | 可實作，但需自行組裝 | 
| MCP / 工具整合 | 以 Connector / MCP 方式整合 | 支援工具呼叫與多模型 | 原生支援 MCP / OpenAPI / A2A | 透過 plug‑in / function 整合 | 
| 部署 / 維運 | 由 Microsoft 全託管 | Azure 全託管 | 本機自架或上雲 | 由應用程式自行負責 | 
| 適用對象 | 業務、IT、一般開發者 | 有雲端經驗的開發團隊 | 進階開發者 | 想在既有程式裡加上 LLM 的開發者 | 

### 什麼情境適合哪一個？

- **「我要快速做一個會聊天、會幫忙查資料或執行流程的助理」**
	→  先看 Copilot Studio，門檻最低、上線最快。

- **「我們有後端團隊，希望把 Agent 當成一個穩定的雲端服務元件」**
	→ 可以考慮 Azure AI Foundry Agent Service，享受託管與企業級安全性。

- **「我們已經有一堆 .NET / Python 系統，只是想讓它們變得更聰明一點」**
	→ 從 Semantic Kernel 下手，把 LLM 能力「嵌進去」既有應用程式。

- **「我們要打造一個可長期演進的多代理平台，未來可能會跟內部系統深度整合」**
	→ 把 Microsoft Agent Framework 當成主軸，必要時再搭配其他服務。

<br>

### 先想清楚「要解決的問題」，再選工具

「我要學的是 AI Agent 還是 Agentic AI？」其實兩者並不是互斥的選項，而是 **不同層級的抽象**：

- 先理解 **AI Agent**：學會怎麼讓一個 Agent 聽懂需求、會用工具、能把事情做完。
- 再往上看 **Agentic AI**：學會怎麼設計整個 workflow、怎麼切出不同專業 Agent、如何協調它們的合作與溝通。

<br>
微軟提供的這四條路線，剛好也從「低程式碼 → 高自訂」排成一個光譜，我們可以：

- 先用 Copilot Studio 做出可以 demo 的東西，
- 再把關鍵部分細部展開到 Microsoft Agent Framework / Semantic Kernel，
- 或是用 Azure AI Foundry Agent Service 接手雲端託管與營運。

<br>

AI Agent 與 Agentic AI 是「不同層級」的設計：前者聚焦於讓單一代理能夠自主使用工具完成任務，後者則著重於多個代理之間的協作與流程編排。實務上建議採取由淺入深的策略：先用 Copilot Studio 或簡單的 Agent 快速驗證想法，確認核心流程與資料來源；接著把關鍵邏輯以 Semantic Kernel 或 Microsoft Agent Framework 封裝、進行測試與版本管理；最後視需求將成熟的元件移入 Azure AI Foundry 等託管平台以滿足安全與運維需求。選擇工具時，應以「要解決的問題、團隊技術能力與營運需求」為優先，從小型 PoC 漸進到 Production 架構，才能最小化風險並累積實作經驗。

