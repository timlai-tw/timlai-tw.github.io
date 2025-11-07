---
title: Azure DevOps Services / Server 使用者授權
date: 2025-04-03 11:36:15
tags: 
 - Azure DevOps
 - 授權
---

<img src="/post/2025/04/azure-devops-licensing-description/affliction512.jpg" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 

Azure DevOps 是一個可以讓我們有效管理軟體開發流程，同時維持良好團隊協作的強大平台，除了可以直接使用雲端 Azure DevOps Services，也可以在地端自建 Azure DevOps Server 讓企業內部環境也能提供相同的服務，但許多人遇到的第一個問題是如何購買授權才能讓成本效益最大化？這篇文章主要協助大家瞭解 Azure DevOps 在授權與定價上的內容。

<br>

<!-- more -->

<br>

## Azure DevOps Services

Azure DevOps 有五個主要功能：
 1. **Azure Boards** (專案管理、看板管理、Scrum / Kanban、討論議題)
 1. **Azure Repos** (程式碼版控 TFVC/git)
 1. **Azure Pipelines** (自動化建置部署)
 1. **Azure Artifacts** (套件管理、自建套件來源)
 1. **Azure Test Plans** (建立與執行測試計劃)

### Basic Plan 與 Basic + Test Plans

{% asset_img Basic_TestPlans.jpg %}

在 Azure DevOps 定價頁面中的 **`Basic Plan`** 包含了前面四項功能，費用是 **USD $6** (每個人/月)，折合台幣就要看當月的匯率計算 (例如目前是 TWD **$191**)；一般來說，在專案團隊裡並不是每個成員都需要建立與管理測試計劃，所以 **Azure Test Plans** 這項功能額外被拆出來計價，若選擇 **`Basic + Test Plans`** 費用則是 **USD $52** (大約 TWD **$1663**)。

### Stakeholder
除了上述兩種存取層級(Access level)之外，還有一種是免費、可使用==有限功能==的 **``Stakeholder``**，比較簡單的來說，這個存取層級可以：
- 建立 / 編輯 / 查詢工作項目
- 檢視 / 核准 pipeline
- 檢視測試計劃

如同它的名稱一樣：想要瞭解專案資訊，但又不需要實際參與開發實作細節(例如：單位主管、使用單位...等)，就不用再額外採購授權，使用 **``Stakeholder``** 這個存取層級就行。若需要 **程式碼版控**、**建立/編輯 pipeline**、**執行測試計劃** 那就不用猶豫了，至少一定需要 **`Basic`** 存取層級才能使用，這些資訊可以參閱下列連結，利用頁面裡的幣別下拉選單、拖曳數量捲軸來即時檢視費用估算，頁面最後還有一個針對不同存取層級的功能比較表，方便大家查閱。

<div class="tips">
<font color="black">
<a href="https://azure.microsoft.com/zh-tw/pricing/details/devops/azure-devops-services/" target="_blank">⁍ Azure DevOps 定價</a>
<br>
<a href="https://learn.microsoft.com/zh-tw/azure/devops/organizations/security/stakeholder-access?view=azure-devops" target="_blank">⁍ 項目關係人存取快速參考</a>
</font>
</div>

### Visual Studio Subscription
若目前已經具備 **Visual Studio monthly subscription** (注意：==訂閱制==才行)，它也包含了 Azure DevOps 的使用授權，基本上可以理解為：**Visual Studio Professional monthly subscription** 包含了 **`Basic Plan`**，而 **Visual Studio Enterprise monthly subscription** 包含了 **`Basic + Test Plans`**，詳細功能支援列表可以參考下列連結。

<div class="tips">
<font color="black">
<a href="https://learn.microsoft.com/zh-tw/azure/devops/organizations/security/access-levels?view=azure-devops" target="_blank">⁍ 關於存取層級</a>
<br>
<a href="https://marketplace.visualstudio.com/items?itemName=ms.vs-professional-monthly" target="_blank">⁍ Visual Studio Professional monthly subscription</a>
<br>
<a href="https://marketplace.visualstudio.com/items?itemName=ms.vs-enterprise-monthly" target="_blank">⁍ Visual Studio Enterprise monthly subscription</a>
</font>
</div>

## Azure DevOps Server
要在地端環境自建 Azure DevOps Server 則需要先準備好 SQL Server 環境，兩者在版本上有相依性，例如：Azure DevOps Server 2022 需要搭配 SQL Server 2019 以上 (不支援 Linux 上的 SQL Server)，其他版本與相關需求建議可以參考下列連結。

<div class="tips">
<font color="black">
<a href="https://learn.microsoft.com/zh-tw/azure/devops/server/requirements?view=azure-devops-2022" target="_blank">⁍ Azure DevOps 內部部署的需求</a>
</font>
</div>

以授權的概念來說，安裝幾台 Azure DevOps Server 就需要幾個 Server CAL，可以透過下列幾種方式取得合法授權：
- 傳統購買 (==3年SA==) Azure DevOps ==Server== CAL
- Visual Studio Professional / Enterprise monthly subscription (包含 Azure DevOps Server CAL)

<br>

地端的使用者授權可以由下列幾種方式取得合法授權：
- 傳統購買 (==3年SA==) Azure DevOps User CAL (包含 **`Basic + Test Plans`** 所有功能)
- Visual Studio Professional / Enterprise monthly subscription (包含 Azure DevOps ==User== CAL)
- Azure DevOps **`Basic`** 或 **`Basic + Test Plans`** (雲端的每人每月購買方式，也包含了地端的使用者授權)

<br>

以上是對於 Azure DevOps Services / Server 授權的整理說明，若大家還有其他不瞭解的地方，歡迎透過下方留言討論。 :)