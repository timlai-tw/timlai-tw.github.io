---
title: Azure DevOps REST API 說明
date: 2025-04-10 10:38:44
tags:
 - Azure DevOps
 - Azure DevOps REST API
 - PAT
 - .NET Client Library
---

<img src="/post/2025/04/azure-devops-rest-api/rest_api.jpg" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 

當我們要自行擴充或串接 Azure DevOps Services / Server 功能時，可以透過 Azure DevOps REST API 來進行客制開發，例如：想要寫一支程式每天發送各專案在版控儲存庫的差異紀錄，或是想要在公司內部表單系統審核後，觸發執行指定的 pipeline ... 族繁不及備載，使用 REST API 透過 HTTP 呼叫的好處是可以不用侷限於特定的程式語言，也方便用 Postman 之類的工具測試，這篇整理了呼叫 Azure DevOps REST API 的前置作業與相關說明，方便大家上手:grin:。

<!-- more -->

<br>

## REST API
使用 Azure DevOps REST API 時，其 URI 規格如下：

```http
VERB https://{instance}/{organization}/{project}/_apis/{area}/{resource}?api-version={version}
```

- **VERB**
  與一般 RESTful API 相同，在串接 Azure DevOps 各種情境時， **<font color='green'>GET</font>** / **<font color='brown'>POST</font>** / **<font color='red'>DELETE</font>** / **<font color='purple'>PATCH</font>** 都有機會用到。
- **instance**
  如果是使用雲端的 Azure DevOps Services 指的就是 <span>dev.azure.com</span>，地端則是 Azure DevOps Server 所在的位址。
- **organization/project**
  取決於 API 作用的範圍，在 URI 裡會有 organization 或 organization/project，例如：使用 API 建立個人權杖 (PAT)，則會是：
  **<font color='brown'>POST</font>** https://vssps.dev.azure.com/=={organization}==/_apis/tokens/pats?api-version=7.2
  或者像是用 API 建立一個工作項目，則是：
  **<font color='brown'>POST</font>** https://dev.azure.com/=={organization}/{project}==/_apis/wit/workitems/${type}?api-version=7.2
- **{area}/{resource}**
  從這邊大概可以看出呼叫的 API 是做哪方面的操作，以上面兩個例子 (建立 PAT、建立 Work item) 來說：
  **<font color='brown'>POST</font>** https://vssps.dev.azure.com/{organization}/_apis/==tokens/pats==?api-version=7.2
  **<font color='brown'>POST</font>** https://dev.azure.com/{organization}/{project}/_apis/==wit/workitems/${type}==?api-version=7.2
- **version**
  通常我們在串接 API 時會明確指定 version，因為每個 TFS / Azure DevOps 版本對於 API 支援的 api-version 也有所不同 (如下表)，如果不指定 api-version 則會自動選用目前產品最新的穩定版本，例如：在 Azure DevOps Server 2019 它會自動取用 5.0 來執行，在不加 api-version 的情況下，若日後升級到 Azure DevOps Server 2022，則會自動取用 7.0 來執行，為維持串接程式正常運作，不會因為 API 版本差異而造成異常，一般我們都會加上指定的 api-version 版號。

> 提醒大家：
> 如果是使用 preview 版本 (例如：**`7.2-preview.3`**)，該 preview 版本會在 **12 週** 後停用，被穩定的正式版本取代。

| Products                    | 1.0 | 2.0 | 3.0 | 4.0 | 5.0 | 6.0 | 7.0 |
|-----------------------------|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Azure DevOps Services       | V   | V   | V   | V   | V   | V   | V   |
| Azure DevOps Server 2022    | V   | V   | V   | V   | V   | V   | V   |
| Azure DevOps Server 2020    | V   | V   | V   | V   | V   | V   | -   |
| Azure DevOps Server 2019    | V   | V   | V   | V   | V   | -   | -   |
| Team Foundation Server 2018 | V   | V   | V   | V   | -   | -   | -   |
| Team Foundation Server 2017 | V   | V   | V   | -   | -   | -   | -   |
| Team Foundation Server 2015 | V   | V   | -   | -   | -   | -   | -   |

## PAT (Personal Access Token)
要使用 Azure DevOps REST API 存取資源前都必需先經過驗證成功才行，建立 **`個人存取權杖 (PAT)`** 可以算是安全又簡便的方式，若想要實作 OAuth 2.0 或其他頁面驗證方式，可以參考下列連結：
<div class="tips">
<font color="black">
<a href="https://learn.microsoft.com/zh-tw/azure/devops/integrate/get-started/authentication/authentication-guidance?toc=%2Fazure%2Fdevops%2Fmarketplace-extensibility%2Ftoc.json&view=azure-devops" target="_blank">⁍ 選擇正確的驗證機制</a>
</font>
</div>

要建立一個 PAT 可以從 Azure DevOps 畫面右上角 **`User settings`** → **`Personal access tokens`** 進入。
{% asset_img pat.png %}

<br>

點選 **`+ New Token`** 按鈕。
{% asset_img pat_1.png %}

<br>

接著就是 PAT 存取期限、授權範圍等相關設定：
- **Organization**
  選擇 **特定的 Organization** 或 **All accessible organizations** (允許存取全部組織)。
- **Expiration (UTC)**
  有效期限可以選擇 **30 / 60 / 90 天**，或是 **Custom defined** (==最長只能一年==)
- **Scopes**
  可以選擇 **Full access** (允許存取全部範圍) 或 **Custom defined** 針對各個功能進一步設定，基於最低權限原則，建議大家可以有意義的命名 token 名稱，並且只勾選必要的 **Scopes** 授權範圍。

建立 PAT 後，僅能 **`Revoke`** (撤銷) 或是 **`Regenerate`** (重新產生)，沒有辦法再用任何方式檢視原本產生的 token 內容，大家記得要妥善保存。

{% asset_img pat_2.png %}

## .NET Client Library
如果是使用 .NET 進行程式開發，還有一個選擇是從 NuGet.org 下載 Client library 使用，套件相依關係如下圖。

{% asset_img dotnet-client-libraries-dependancy-diagram.jpg %}

<br>

最新版本可以由 NuGet.org 查詢結果找到： https://www.nuget.org/packages?q=azure+devops+.net ，套件和 Azure DevOps 版本一樣有相依性， 使用前要留意一下目前環境的 TFS / Azure DevOps Server 版本，例如：Azure DevOps Server 2019 只能使用到套件 16.143.x 版。

| 套件版本   | Azure DevOps 版本                        |
|-----------|------------------------------------------|
| 16.205.x  | versions >= Azure DevOps Server 2022     |
| 16.170.x  | versions >= Azure DevOps Server 2020     |
| 16.153.x  | versions >= Azure DevOps Server 2019 Update 1 |
| 16.143.x  | versions >= Azure DevOps Server 2019     |
| 15.131.x  | versions >= TFS 2018 Update 1            |
| 15.112.x  | versions >= TFS 2017 Update 1            |
| 14.102.x  | versions >= TFS 2015 Update 3            |
| 14.95.x   | versions >= TFS 2015 Update 2            |
| 14.89.x   | versions >= TFS 2015 Update 1            |
| 14.83.x   | versions >= TFS 2015                     |

在實務開發上還是得花些時間先瞭解套件定義了哪些 class、提供了什麼 function，這部份可以參考官方說明和 github sample code，如果有特殊需求或環境考量需要使用的話，可以參考下列連結：

<div class="tips">
<font color="black">
<a href="https://learn.microsoft.com/zh-tw/azure/devops/integrate/concepts/dotnet-client-libraries?view=azure-devops&viewFallbackFrom=vsts" target="_blank">⁍ .NET 用戶端程式庫</a><br>
<a href="https://learn.microsoft.com/zh-tw/azure/devops/integrate/get-started/client-libraries/samples?view=azure-devops" target="_blank">⁍ C# 用戶端連結庫範例</a><br>
<a href="https://github.com/microsoft/azure-devops-dotnet-samples" target="_blank">⁍ .NET samples for Azure DevOps</a>
</font>
</div>

(:information_desk_person: 看完上面連結內容有沒有覺得還是使用 REST API 比較方便 :sparkling_heart:)

