---
title: Azure DevOps MCP Server
date: 2025-06-18 08:39:23
tags: 
 - Azure DevOps
 - MCP
---

<img src="/post/2025/06/azure-devops-mcp-server/mcp.png" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 

隨著 AI 技術的蓬勃發展，軟體開發與運維 (DevOps) 流程正迎來一場革命性的轉變。傳統的 DevOps 強調自動化、持續整合與持續交付，但在面對日益複雜的專案協作與工具整合時，團隊仍需投入大量人力進行手動操作與跨系統協調。這時，AI 驅動的自動化與智慧化流程成為新一代 DevOps 的核心訴求。Model Context Protocol（MCP，模型上下文協議）正是在這樣的背景下誕生，為大型語言模型（LLM）與外部工具之間建立一套安全、模組化且可擴充的溝通標準。自微軟在上個月 Microsoft Build 2025 開發者大會宣佈會加入對 MCP 的支援，這個月 Azure DevOps MCP Server 也已公開預覽 (Public Preview)，本文將介紹 Azure DevOps MCP Server 提供了哪些工具。

<!-- 結合 MCP 的 Azure DevOps MCP Server，讓 AI 助理（如 GitHub Copilot）能直接理解專案脈絡、查詢與操作 DevOps 資源，甚至根據自然語言指令自動執行工作，徹底改變開發者與工具的互動模式。本文將介紹 MCP 的由來、Azure DevOps MCP 的分類與工具，並詳細說明各工具的實際用途，協助你掌握這項前瞻技術如何為團隊帶來更高的生產力與協作效率。 -->
<br>

<!-- more -->

<br>

## MCP 的由來
MCP（Model Context Protocol）最初由 Anthropic 在 2024 年 11 月提出，是一套專為大型語言模型設計的開放式標準協議。其核心目標在於讓 LLM 能夠安全、模組化且可擴充地存取外部工具與資料來源。MCP 的設計理念類似於 API，但傳統 API 是給開發者使用，強調結構化資料與嚴謹格式；而 MCP 則是為 AI 模型量身打造，讓模型能用自然語言理解上下文，並以「對話」方式與外部系統互動。對使用者而言，整個過程更貼近人類溝通習慣，MCP 讓 LLM 不只是資訊生成者，更能成為任務執行者，推動開發流程從「使用工具」進化為「與工具對話」。

## MCP 和 Function Calling 的不同
| 功能 | Function Calling | MCP |
| :-- | :-- | :-- |
| 主要目的 | 把自然語言請求轉成結構化函數呼叫，由應用程式執行 | 標準化 LLM 與外部工具/資料源的連接與執行流程 |
| 控制權 | LLM 供應商（如：OpenAI、Anthropic） | 外部系統（MCP Server/Client） |
| 輸出格式 | 不同 LLM 廠商格式各異（通常為 JSON） | 統一採用標準協議（如：JSON-RPC） |
| 擴展性/互通性 | 需為每個工具自訂整合，跨平台困難 | 一次整合，多方兼容，工具可被多個 LLM 共用 |
| 標準化 | 無統一標準，各家 LLM 格式不同 | 統一協議，保證跨工具、跨平台一致性 |
| 適用場景 | 單一應用、簡單任務、需嚴格控制副作用 | 多工具、多平台、大規模協作與自動化 |

MCP 並非為了取代 Function Calling 而推出，兩者是互補關係：
 - **Function Calling**：讓 LLM 能夠「決定」要做什麼（下訂單）。
 - **MCP**：讓這些決策能夠在不同工具、平台間「被執行」且流程標準化（執行訂單）。

實際應用中，LLM 會先用 Function Calling 決定要呼叫哪個工具、什麼參數，然後 MCP 負責將這個請求標準化、分發到對應的 MCP Server 執行，並將結果回傳。其他關於 MCP 更多的詳細資訊，可以參考下列 Anthropic 官網內容：

<div class="tips">
<font color="black">
<a href="https://docs.anthropic.com/zh-TW/docs/agents-and-tools/mcp" target="_blank">⁍ 模型上下文協議 (MCP)</a><br>
<a href="https://github.com/modelcontextprotocol" target="_blank">⁍ GitHub | Model Context Protocol</a>
</font>
</div>

## Azure DevOps MCP Server
Azure DevOps MCP Server 以 MCP 為基礎，提供一套可由 AI 助手（如：GitHub Copilot）透過自然語言存取與操作 Azure DevOps 相關服務的工具集合。這些工具依功能可分類如下幾種：

## 🧿 核心 (Core)

| 工具名稱 | 功能描述 |
| :-- | :-- |
| core_list_project_teams | 取得指定專案的團隊清單 |
| core_list_projects | 取得組織下所有專案清單 |

## ⚒️ 工作 (Work)

| 工具名稱 | 功能描述 |
| :-- | :-- |
| work_list_team_iterations | 取得專案中某團隊的所有迭代清單 |
| work_create_iterations | 在指定專案中建立新迭代 |
| work_assign_iterations | 將既有迭代指派給專案中的團隊 |

## 📅 工作項目 (Work Items)

| 工具名稱 | 功能描述 |
| :-- | :-- |
| wit_my_work_items | 取得目前使用者相關的工作項目 |
| wit_list_backlogs | 取得專案與團隊的待辦清單 |
| wit_list_backlog_work_items | 取得指定專案、團隊及類別的待辦工作項目 |
| wit_get_work_item | 依 ID 取得單一工作項目 |
| wit_get_work_items_batch_by_ids | 批次取得多個工作項目 |
| wit_update_work_item | 依 ID 更新工作項目欄位 |
| wit_create_work_item | 在指定專案建立新工作項目 |
| wit_list_work_item_comments | 取得指定工作項目的留言 |
| wit_get_work_items_for_iteration | 取得指定迭代的所有工作項目 |
| wit_add_work_item_comment | 為指定工作項目新增留言 |
| wit_add_child_work_item | 由父項目建立子工作項目 |
| wit_link_work_item_to_pull_request | 將工作項目連結至現有 Pull Request |
| wit_get_work_item_type | 取得指定工作項目類型 |
| wit_get_query | 依 ID 或路徑取得查詢 |
| wit_get_query_results_by_id | 依查詢 ID 取得查詢結果 |
| wit_update_work_items_batch | 批次更新多個工作項目 |
| wit_close_and_link_workitem_duplicates | 關閉重複的工作項目 |
| wit_work_items_link | 批次連結多個工作項目 |

## 📁 原始碼儲存庫 (Repositories)

| 工具名稱 | 功能描述 |
| :-- | :-- |
| repo_list_repos_by_project | 取得專案下所有儲存庫 |
| repo_list_pull_requests_by_repo | 取得指定儲存庫的 Pull Requests |
| repo_list_pull_requests_by_project | 取得專案下所有 Pull Requests |
| repo_list_branches_by_repo | 取得指定儲存庫的分支 |
| repo_list_my_branches_by_repo | 取得指定儲存庫中屬於自己的分支 |
| repo_list_pull_request_threads | 取得 Pull Request 的討論串 |
| repo_list_pull_request_thread_comments | 取得討論串中的留言 |
| repo_get_repo_by_name_or_id | 依名稱或 ID 取得儲存庫 |
| repo_get_branch_by_name | 依名稱取得分支 |
| repo_get_pull_request_by_id | 依 ID 取得 Pull Request |
| repo_create_pull_request | 建立新的 Pull Request |
| repo_update_pull_request_status | 更新 Pull Request 狀態（啟用或放棄） |
| repo_reply_to_comment | 回覆 Pull Request 的指定留言 |
| repo_resolve_comment | 解決 Pull Request 討論串中的留言 |

## 🛰️ 建置 (Builds)

| 工具名稱 | 功能描述 |
| :-- | :-- |
| build_get_definitions | 取得專案的建置定義清單 |
| build_get_definition_revisions | 取得指定建置定義的歷史版本 |
| build_get_builds | 取得專案的建置清單 |
| build_get_log | 取得指定建置的日誌 |
| build_get_log_by_id | 依日誌 ID 取得建置日誌 |
| build_get_changes | 取得指定建置的異動 |
| build_run_build | 觸發指定定義的新建置 |
| build_get_status | 取得指定建置的狀態 |

## 🚀 發行 (Releases)

| 工具名稱 | 功能描述 |
| :-- | :-- |
| release_get_definitions | 取得專案的發行定義清單 |
| release_get_releases | 取得專案的發行清單 |

## 🧪 測試計畫 (Test Plans)

| 工具名稱 | 功能描述 |
| :-- | :-- |
| testplan_create_test_plan | 建立新的測試計畫 |
| testplan_create_test_case | 建立新的測試案例 |
| testplan_add_test_cases_to_suite | 將既有測試案例加入測試套件 |
| testplan_list_test_plans | 分頁取得專案的測試計畫清單，可過濾啟用狀態及細節 |
| testplan_list_test_cases | 取得測試計畫中的測試案例 |
| testplan_show_test_results_from_build_id | 依建置 ID 取得測試結果 |

## 🔎 搜尋 (Search)

| 工具名稱 | 功能描述 |
| :-- | :-- |
| search_code | 依關鍵字搜尋原始碼 |
| search_wiki | 依關鍵字搜尋 Wiki |
| search_workitem | 依關鍵字搜尋工作項目 |

## VSCode 的 MCP 設定

在 VSCode 中要設定 MCP Server 可以按 **`Ctrl + ,`** 打開 Settings 畫面，然後點選右上角 Open Settings 圖示，加入下列設定區塊即可，其中 ado_org 代表 Azure DevOps Services 的 Orgnization 名稱，運行 MCP Server 時會提示在 VSCode 上方讓使用者輸入設定。

{% asset_img open_settings.png %}
<br>
{% asset_img mcp_setting.png %}

```yaml
{
   "inputs": [
     {
         "id": "ado_org",
         "type": "promptString",
         "description": "Azure DevOps organization name  (e.g. 'contoso')"
     }
   ],
   "servers": {
     "ado": {
         "type": "stdio",
         "command": "npx",
         "args": [
             "-y",
             "@azure-devops/mcp",
             "${input:ado_org}"
         ]
      }
   }
}
```
<br>

而在使用前也必需先以 **`az login`** 登入帳號取得使用權限，才能讓 Azure DevOps MCP Server 正常存取資源，要運行或停止設定檔的 MCP Server 僅需要在存檔後，每個 MCP Server 名稱上方就會出現 Start/Stop 的字樣可以點選。

<br>
{% asset_img az_login.png %}

<br>
{% asset_img start_mcp.png %}

<br>
完成 settings.json 的設定並啟動 ado 這個 MCP Server 後，我們就可以在 GitHub Copilot 的 Agent mode 中調用前述整理的各項工具。在對話過程中，GitHub Copilot 會同時列出目前可用的工具清單，讓所選用的模型 (如：GPT-4o) 能根據指令調用相關工具，並將執行結果回傳給使用者，協助回覆各類查詢。

<br>
{% asset_img agent_mode.png %}

## 結語

有了 Azure DevOps MCP Server 提供的工具後，我們就可以透過對話的方式來互動取得 Azure DevOps Services 相關資訊，並自動執行各種開發、協作與運維任務，讓 AI 助理真正成為團隊的智慧夥伴。MCP 標準的導入不僅大幅降低了跨系統整合的門檻，也讓開發流程更具彈性與擴展性，目前 Azure DevOps MCP Server 僅支援雲端的 Azure DevOps Services，尚不支援地端 Azure DevOps Server 的存取方式 (依目前得知的消息，未來幾個月內也不會提供)，但其實要實作 MCP Server 來自己串接這一段也不是太困難的問題，後續有機會再開一篇文章向大家說明。

<div class="tips">
<font color="black">
<a href="https://github.com/microsoft/mcp" target="_blank">⁍ GitHub | mcp</a><br>
<a href="https://github.com/microsoft/azure-devops-mcp" target="_blank">⁍ GitHub | azure-devops-mcp</a><br>
<a href="https://learn.microsoft.com/en-us/azure/developer/azure-mcp-server/tools/" target="_blank">⁍ What are the Azure MCP Server tools?</a>
</font>
</div>