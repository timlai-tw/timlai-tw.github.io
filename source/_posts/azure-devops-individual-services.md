---
title: Azure DevOps Services 個別服務
date: 2025-04-06 21:02:38
tags:
 - Azure DevOps
 - 授權
---

<img src="/post/2025/04/azure-devops-individual-services/individual_services.jpg" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 

[上一篇文章](/post/2024/09/azure-devops-licensing-description)提到了 Azure DevOps 雲端和地端的使用者授權，當我們建立私有專案 (Private project) 且成員數量在 5 個人以下時，甚至可以直接免費使用，從第 6 個人起才需要計價，並具有 1 Free Microsoft-hosted CI/CD 和 1 Free Self-hosted CI/CD 以及 Azure Artifacts 2GB 可用儲存容量。在實務使用情境較複雜時，很有可能就需要再增加個別服務的用量 (例如：Azure Pipelines 平行作業數量)，這次就帶大家瞭解這些個別服務計價的方式。

<!-- more -->

<br>

## Azure Pipelines
不論我們在 Azure Pipelines 使用文字介面或是圖形介面 (classic editor) 建立 pipeline 腳本內容，其中有個 **`Agent pools`** 選項是必要設定，它是用來決定執行階段要把工作 (job) 傳遞到哪個 pool 裡，尋找閒置且符合條件的 **Azure Pipelines agents** 執行個體來執行腳本內容，在 Azure DevOps Services 預設有一個 Microsoft-hosted agent 每個月可以免費使用 1800 分鐘，==但預設無法直接使用==，需要填寫 **[Azure DevOps Parallelism Request](https://aka.ms/azpipelines-parallelism-request)** 表單申請審核通過才行，否則在執行的時候會出現如下錯誤：

{% asset_img  azpipelines-parallelism-request.jpg %}

當然我們也可以自己準備機器，在上面安裝 Azure Pipelines agents 程式 (支援 Windows / Linux / macOS)，這就屬於 Self-hosted agent。由於是自己準備的執行個體，所以使用時數不受限制，但每個工作 (job) 都是經由佇列 (queue) 的方式排隊一個一個執行，若需要同時間處理一個以上的工作 (job)，則需要再增加執行個體才行 (不論是 Microsoft-hosted 或是 Self-hosted)，基於這樣的概念，以下就是我們所關心的計價標準：
- 每一個 Microsoft-hosted 每個月有 1800 分鐘可以使用。
- 每增加一個額外的 Microsoft-hosted CI/CD 每個月是 USD $40 (大約 **TWD $1279**)。
- 每增加一個額外的 Self-hosted CI/CD 每個月是 USD $15 (大約 **TWD $479**)。
- ==如果在地端自建 Azure DevOps Server 則僅能使用 Self-hosted agent，需要更多的平行作業就自己多準備幾個執行個體，不用再額外採購什麼授權。==

## Azure Artifacts
在軟體專案開發過程中，多少都會用到一些公司內部自行開發，或是外部來源提供的套件，一般常見的套件來源像是：NuGet、npm、Maven、Python...等，Azure Artifacts 可以將這些外部提供來源設為上游來源 (Upstream sources)，或是自己建立一個套件提供來源 (Feeds)，讓專案團隊成員在內部環境取用，儲存容量在 2GB 以內是免費，若超過則依下列區間以量計價：


| 儲存容量 | 每GB (USD) | 每GB (TWD) |
|---------|------------|------------|
| 0 - 2 GB | 免費 | 免費 |
| 2 - 10 GB | $2 | 大約 $63.98 |
| 10 - 100 GB | $1 | 大約 $31.99 |
| 100 - 1000 GB | $0.50 | 大約 $16.00 |
| 1000 GB 以上 | $0.25 | 大約 NT$8.00 |

## GitHub Advanced Security for Azure DevOps
以往我們想要對專案程式碼執行原碼檢測、套件掃描等作業，首先要考量的可能是產品的價錢，或是委外檢測的費用，比較克難一點還可以尋找免費的掃描檢測工具(例如只是想要出個報告 :see_no_evil: :speak_no_evil:)，若想要整合到 CI 流程裡，還要得額外花一番功夫，但基本上只要可以透過 command line 來執行，都是可以用 Azure Pipelines 整合起來的。而自從 **GitHub Advanced Security for Azure DevOps** 正式啟用後，省掉了這部份不少麻煩，除了原生的與 Azure DevOps Services 整合 ==(不包含地端 Azure DevOps Server)==，也可以與 **Microsoft Defender for Cloud** 串接，它可以做到：
- **Code Scanning**：找出原始程式碼中的漏洞並提供修復指導。
- **Secret Scanning**：識別 secret 並阻止開發人員將 secret 推送到程式碼儲存庫。
- **Dependency Scanning**：發現 open-source 依賴項目的漏洞，並為開發人員自動發出更新警報。

在 Azure Pipelines 也提供了下列對應的 task 以便我們完善整個 **`DevSecOps`** 流程：
{% asset_img github_acvanced_security.jpg %}

這個功能可以由每個 git 的儲存庫 (repositry) 個別設定是否啟用 (不支援 TFVC 版控)，計價的原則是：每一個活躍的提交者 (active committer) 每個月 **USD $49** (大約 **TWD $1567**)，在==同一個 Azure Subscription== 中，儲存庫過去 **`90天`** 的紀錄中，同一個提交者(committer)在不同專案提交程式碼只會算 1 個數量，不會被重複計價。當我們在 git 儲存庫開啟 **`Advanced Security`** 功能時，Azure DevOps 在畫面上也會先幫我們基於上述原則計算有多少使用者會被計費，確定要使用再點選 **`Begin billing`** 即可：
{% asset_img unique_committers.jpg %}

如同 Azure 各種服務的計價方式，都是**隨付即用 (pay-as-you-go)**，啟用 **GitHub Advanced Security for Azure DevOps** 搭配 CI 流程可以讓我們在開發階段及早發現問題與漏洞，有效減少技術債和後期投入修改的人力成本，跟測試左移 (Shift-Left Testing) 的概念有點雷同，也可以省下大筆購入源碼檢測軟體的費用，大家可以參考看看。

<div class="tips">
<font color="black">
<a href="https://azure.microsoft.com/zh-tw/pricing/details/devops/azure-devops-services/" target="_blank">⁍ Azure DevOps 定價</a><br>
<a href="https://azure.microsoft.com/zh-tw/products/devops/github-advanced-security" target="_blank">⁍ 適用於 Azure DevOps 的 GitHub Advanced Security</a><br>
<a href="https://learn.microsoft.com/zh-tw/azure/devops/repos/security/configure-github-advanced-security-features?view=azure-devops&tabs=yaml" target="_blank">⁍ 設定適用於 Azure DevOps 的 GitHub Advanced Security</a>
</font>
</div>