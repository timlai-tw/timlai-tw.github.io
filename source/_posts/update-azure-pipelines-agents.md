---
title: Azure Pipelines Agents 代理程式
date: 2025-06-08 23:05:46
tags:
 - Azure DevOps
 - Azure Pipelines Agents
---

<img src="/post/2025/06/update-azure-pipelines-agents/azure_pipelines_agents.png" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 

Azure Pipelines Agents 是現代 DevOps 流程中不可或缺的基礎組件。隨著企業加速數位轉型，持續整合（CI）與持續交付（CD）成為團隊自動化建置、測試與部署的重要手段。Azure Pipelines 做為 Azure DevOps 的核心服務之一，透過代理程式（Agent）來執行各式自動化工作。無論是雲端託管或自我託管，代理程式皆負責與 Azure DevOps Server 或 Azure DevOps Services 溝通，協助團隊將程式碼快速且安全地部署至各種環境。近年來，隨著代理程式版本的演進，功能與安全性也大幅提升，特別是在 3.x 與 4.x 之間有諸多關鍵差異，本文將介紹 Azure Pipelines Agents 的基本概念、版本比較、更新方式與工作目錄結構。
<br>

<!-- more -->

<br>

## 什麼是 Azure Pipelines Agents

Azure Pipelines Agents 代理程式是負責執行 Azure Pipelines 工作（如建置、測試、部署）的運算節點。當您在 Azure DevOps 中建立 CI/CD 流程時，實際負責操作指令、拉取程式碼、執行腳本與產生建置產物的，就是這些代理程式。

**代理程式類型：**

- **Microsoft-Hosted**：由微軟提供與維護，適合雲端開發。
- **Self-Hosted**：由使用者自行安裝於 VM、實體機或容器，適合需要存取內部資源或自訂環境的需求。
- **Managed DevOps Pool**：Azure 提供的完全受控虛擬機或容器資源。
- **Azure Virtual Machine Scale Sets**：可自動擴展的自我託管代理程式，適合彈性需求。

{% asset_img agent-topologies-tfs.png %}

不論是使用 Azure DevOps Services 或是自建 Azure DevOps Server，代理程式都是使用 HTTPS(443) 來進行下載作業、回報狀態，並確保敏感資訊透過加密方式傳輸。

<div class="tips">
<font color="black">
<a href="https://learn.microsoft.com/zh-tw/azure/devops/managed-devops-pools/architecture-overview?view=azure-devops" target="_blank">⁍ Microsoft受控 DevOps 集區架構概觀
</a><br>
<a href="https://learn.microsoft.com/zh-tw/azure/virtual-machine-scale-sets/overview" target="_blank">⁍ 什麼是虛擬機器擴展集</a><br>
<a href="https://learn.microsoft.com/zh-tw/azure/devops/managed-devops-pools/migrate-from-scale-set-agents?view=azure-devops" target="_blank">⁍ 比較管理的 DevOps 群組與 Azure 虛擴展集的代理</a>
</font>
</div>

<br>

## Azure Pipelines 2.x、3.x、4.x 版本差異比較

根據官方文件，Azure Pipelines Agents 2.x、3.x 與 4.x 版本在底層平台、作業系統支援範圍、安全性、升級策略等多方面皆有顯著差異。以下整理三個版本的主要比較：


| 項目 | 2.x 代理程式 | 3.x 代理程式 | 4.x 代理程式 |
| :-- | :-- | :-- | :-- |
| 核心平台 | .NET Core 3.1 | .NET 6 | .NET 8 |
| 發行時間 | 2021 年以前 | 2023 年 3 月 | 2024 年 10 月 |
| 作業系統支援 | 支援較舊的作業系統 | 支援較新 OS，淘汰部分舊版 | 僅支援最新 OS，淘汰更多舊版 |
| 主要支援 OS | Windows 7/8.1、舊版 Linux | Windows 10/11、Ubuntu 18+ | Windows 10/11、Ubuntu 20+ |
| macOS 支援 | macOS 10.15 及以上 | macOS 10.15~14 (含 ARM64) | macOS 13~15 (含 ARM64) |
| Linux 支援 | 舊版 Linux (Debian 10 等) | Debian 10+、Ubuntu 16+ | Debian 12、Ubuntu 20+ |
| 安全性維護 | 僅針對重大問題個案處理 | 僅針對 3.x 修補 | 僅針對 4.x 修補 |
| 依賴平台 | 需安裝 .NET Core 3.1 | .NET 6 已內建於代理程式中 | .NET 8 已內建於代理程式中 |


### 升級與相容性注意事項

2.x、3.x、4.x 代理程式的主要差異在於底層 .NET 平台、支援的作業系統範圍、相容性與維護策略，若代理程式執行於不支援的作業系統，將無法升級至新版，且執行管線時會出現錯誤提示，需先升級作業系統才能繼續。每一代代理程式僅針對自身版本進行安全性修補，建議維運團隊定期檢查代理程式與作業系統版本，確保 DevOps 流程穩定且安全。

<div class="tips">
<font color="black">
<a href="https://learn.microsoft.com/zh-tw/azure/devops/pipelines/agents/agents?view=azure-devops-2022&tabs=yaml%2Cbrowser#can-i-update-my-agents-that-are-part-of-an-azure-devops-server-pool" target="_blank">⁍ Azure Pipelines 代理程式</a><br>
<a href="https://learn.microsoft.com/zh-tw/azure/devops/pipelines/agents/v3-agent?view=azure-devops" target="_blank">⁍ 代理程式軟體第 3 版</a><br>
<a href="https://learn.microsoft.com/zh-tw/azure/devops/pipelines/agents/v4-agent?view=azure-devops#upgrade-to-4x-agent-on-supported-operating-systems" target="_blank">⁍ 代理程式軟體第 4 版</a>
</font>
</div>

<br>

## Azure DevOps Server 更新代理程式來源方式

若您在地端環境 Azure DevOps Server 中管理 Self-Hosted Agent，除了透過網路自動更新外，也可以手動指定代理程式的安裝來源。只需將新版代理程式的壓縮檔（.zip）放置於伺服器的 **`%ProgramData%\Microsoft\Azure DevOps\Agents`** 目錄內，代理程式便會自動從該路徑取得更新檔案（如下圖 4.258.1）；若將目錄中的檔案清空，則會顯示目前 Azure DevOps Server 預設的下載版本，例如：Azure DevOps Server 2022.2 預設版本是 3.240.0。

{% asset_img agents_folder.png %}
<br>
{% asset_img get_the_agent.png %}

> 若要更新 Azure DevOps Server 的代理程式，請將新版本壓縮檔放到 ==**%ProgramData%\Microsoft\Azure DevOps\Agents**== 資料夾，代理程式會自動偵測並完成更新

關於 Azure Pipelines Agent 發行過的所有版本都可以在 GitHub 的 Release 紀錄中找到，可以參考下列連結。

<div class="tips">
<font color="black">
<a href="https://github.com/microsoft/azure-pipelines-agent/releases" target="_blank">⁍ GitHub | Azure Pipelines Agent 🚀</a>
</font>
</div>

<br>

## Azure Pipelines Agents 工作目錄結構

代理程式在執行 pipeline 作業時，會建立一套標準化的目錄結構，用於儲存原始碼、建置產物、測試結果等資料。以下為主要目錄說明：

| 目錄名稱 | 說明 | 範例（Windows） | 預設變數 |
| :-- | :-- | :-- | :-- |
| 代理主目錄 | 代理程式安裝位置 | **==C:\agent==** | Agent.HomeDirectory |
| 工作目錄 | 儲存原始碼、建置產物、專案等 | C:\agent\ **==_work==** | Agent.WorkFolder |
| 建置目錄/工作區 | 管線作業執行位置 | C:\agent\_work\ **==1==** | Agent.BuildDirectory |
| s 目錄 | 檢出後的原始碼目錄 | C:\agent\_work\1\ **==s==** | Build.SourcesDirectory |
| b 目錄 | 建置產物目錄 | C:\agent\_work\1\ **==b==** | Build.BinariesDirectory |
| a 目錄 | 工件暫存目錄 | C:\agent\_work\1\ **==a==** | Build.StagingDirectory |
| TestResults | 測試結果目錄 | C:\agent\_work\1\ **==TestResults==** | Common.TestResultsDirectory |

- **代理主目錄**：安裝代理程式的根目錄。
- **工作目錄**：所有作業的儲存空間，會依每次執行建立子目錄。
- **建置/工作區目錄**：每次 pipeline 執行時的專屬目錄。
- **s/b/a/TestResults**：分別對應原始碼、建置產物、工件暫存與測試結果。

在 Self-Hosted 執行環境，這些目錄會在每次執行間保留，僅暫存與測試結果目錄會自動清理；若為 Microsoft-Hosted 則每次皆為全新環境，瞭解各目錄的用途與預設變數名稱對於後續的 pipeline 撰寫有很大的助益，在 Azure Pipelines 中預先定義的變數可以參考下列連結。

<div class="tips">
<font color="black">
<a href="https://learn.microsoft.com/zh-tw/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml" target="_blank">⁍ 使用預先定義的變數</a>
</font>
</div>

<br>

## 結語

Azure Pipelines Agents 是實現現代 DevOps 流程的關鍵，無論是雲端或地端部署，選擇合適的代理程式版本與管理方式，能大幅提升自動化流程的安全性與效率，維運團隊可以持續關注代理程式版本更新，並善用官方提供的更新機制，打造穩健且高效的 CI/CD 環境。

## 後續更新

今天剛好有客戶詢問如何更新 Azure Pipelines Agent 版本，實務上如前文所述，只需先從 GitHub 下載所需的代理程式版本，放到 Azure DevOps Server 指定的資料夾，接著在 Web 介面點選 **`Update all agents`** 按鈕，即可批次更新指定 pool 內所有狀態為 Online 的 Agent。整個更新過程無需停機，Agent 會自動收到更新指令、下載並安裝新版本，完成後會自動重啟服務（大約僅離線 10 秒），即可輕鬆完成批次更新作業。

{% asset_img update_agent.png %}