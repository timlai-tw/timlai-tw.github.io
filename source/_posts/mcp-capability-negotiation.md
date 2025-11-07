---
title: 關於 MCP 的 Capability Negotiation
date: 2025-07-05 11:40:09
tags:
 - MCP
---

<img src="/post/2025/07/mcp-capability-negotiation/robot.jpg" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 

在當前 AI 技術快速發展的時代，MCP (Model Context Protocol) 為大型語言模型帶來更多元且強大的擴展能力。透過 MCP 讓開發者能使 IDE 工具與語言模型自然結合，實現語意驅動的互動體驗。MCP 架構中包含 MCP Server、MCP Host 與 MCP Client 三大核心角色，彼此協同合作並透過能力協商（Capability Negotiation）機制，確保雙方能根據自身能力與需求，協調出最佳的功能組合與協作方式。本文將以[上一篇文章](/post/2025/06/azure-devops-mcp-server/)為例，說明其背景運作流程。

<!-- more -->

<br>

## Core Components 核心組件

MCP 採用 client-host-server 的架構設計，其中一個 host 可以同時管理多個 client instance，並與 MCP Server 建立一對一的連線，藉此維持安全邊界並隔離各自的關注點。以 VSCode 安裝 GitHub Copilot Extension 並連接 Azure DevOps MCP Server 為例，VSCode 扮演 host 角色，而 GitHub Copilot 則是 client，而 Azure DevOps MCP Server 則是 server。每個 client 透過 host 啟動並與 server 進行能力協商，確保各自的功能與需求能被正確識別與協調，進而實現安全且高效的協作。

正如 MCP 官方文件所示意的架構圖，我們可以在 VSCode 中透過 GitHub Copilot 與不同的模型進行互動。只要註冊並設定不同的 MCP Server，就能根據需求取得各式各樣的工具，進一步擴展大型語言模型的應用範圍與處理能力。目前要開發 MCP Server 的門檻已經大幅降低，在 Python / Node.js / Java / Kotlin / C# 都有很豐富的實作範例可以參考，可以參考下列整理連結。

{% asset_img core_components.png %}

<br>
<div class="tips">
<font color="black">
<a href="https://modelcontextprotocol.io/quickstart/server" target="_blank">⁍ Quickstart - For MCP Server Developers</a><br>
<a href="https://github.com/modelcontextprotocol/csharp-sdk" target="_blank">⁍ GitHub | csharp-sdk</a><br>
<a href="https://learn.microsoft.com/zh-tw/dotnet/ai/get-started-mcp" target="_blank">⁍ 開始使用 .NET AI 和模型內容通訊協定</a><br>
<a href="https://learn.microsoft.com/zh-tw/dotnet/ai/quickstarts/build-mcp-server" target="_blank">⁍ 使用 .NET 建立並連線到最小的 MCP 伺服器</a><br>
</font>
</div>

## Capability Negotiation 能力協商

在 MCP 官方說明文件裡有一張關於 Capability Negotiation 的循序圖畫得很詳細，大家想要瞭解可以參考下方連結，這邊我就以上次的情境舉例：描述使用者透過 VSCode 及 GitHub Copilot 選擇使用 gpt-4o 模型，透過 Azure DevOps MCP Server 與 Azure DevOps Services 溝通，查詢組織專案列表的完整流程。大家可以先看一下循序圖中有哪些角色，再試著由上往下依序閱讀，完整說明在圖片下面。

<div class="tips">
<font color="black">
<a href="https://modelcontextprotocol.io/specification/2025-06-18/architecture#capability-negotiation" target="_blank">⁍ MCP - Architecture | Capacity Negotiation</a><br>
</font>
</div>
<br>

{% asset_img capability_negotiation.png %}

### 角色說明
- User（使用者）：在 VSCode 輸入查詢指令的人。
- MCP Host（VSCode）：負責接收使用者指令，與其他元件溝通的 IDE 工具。
- MCP Client（GitHub Copilot）：協助分析指令、整合工具，並與 AI 互動的中介。
- gpt-4o：負責理解指令、產生工具操作建議與自然語言回應的 AI 模型。
- Azure DevOps MCP Server：負責與 Azure DevOps 溝通的 MCP 伺服器。
- Azure DevOps：實際儲存專案資料的雲端平台 (Azure DevOps Services)。

### 步驟流程說明
#### 1. 建立工具清單（首次連線時）
- MCP Client 會先建立一份工具清單（例如：有哪些查詢指令可用），並把這些資訊傳給 gpt-4o。
- gpt-4o 準備好解析工具需求，回傳每個工具的詳細資料（名稱、參數、用途等）給 MCP Client。
- MCP Client 把這些工具相關資料暫存下來，方便後續使用。

#### 2. 使用者輸入查詢
- User 在 VSCode 輸入像「組織有哪些專案？」這樣的問題。
- MCP Host 把這個問題傳給 MCP Client。

#### 3. 問題分析與轉換
- MCP Client 將指令及上下文資訊傳送給 gpt-4o。
- gpt-4o 解析後發現需要查詢 Azure DevOps 的資料，於是產生對應的 MCP 指令（例如 list_projects）。

#### 4. 發送查詢請求
- MCP Client 把 list_projects 的請求發送給 MCP Host。
- MCP Host 再把這個請求傳給 MCP Server。

#### 5. 與 Azure DevOps 溝通
- MCP Server 收到請求後，轉換成 Azure DevOps 的 REST API 呼叫。
- Azure DevOps 回傳專案列表資料給 MCP Server。

#### 6. 結果回傳與整合
- MCP Server 將查詢結果回傳給 MCP Host。
- MCP Host 把結果傳給 MCP Client。
- MCP Client 整合資料，並將查詢結果及相關內容傳給 gpt-4o。

#### 7. 生成自然語言回應
- gpt-4o 把查詢到的專案資料，轉換成自然語言答案。
- MCP Client 將這個答案封裝好，傳回 MCP Host。
- MCP Host 最後把結果顯示在 VSCode 介面上，讓 User 看到。

整個流程就是：

==使用者發問 → VSCode 接收 → GitHub Copilot 分析 → gpt-4o 理解並產生查詢指令 → Azure DevOps 查詢 → 回傳結果 → gpt-4o 產生易懂的自然語言回應 → 顯示在 VSCode==
每個角色都扮演特定溝通橋樑，確保使用者的問題能被正確理解並得到回覆。

## 總結
MCP 的能力協商機制讓各個元件能根據自身特性協調最佳合作方式，確保安全、彈性與高效的互動體驗。透過明確的角色分工與標準化流程，開發者能更容易串接多元工具與服務，擴展 AI 應用場景。未來隨著 MCP 生態系持續發展，將有助於推動語意驅動開發與 AI 協作的普及。
