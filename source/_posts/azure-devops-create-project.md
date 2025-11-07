---
title: 使用 Azure DevOps REST API 建立專案
date: 2025-04-13 09:30:58
tags:
 - Azure DevOps
 - Azure DevOps REST API
---

<img src="/post/2025/04/azure-devops-create-project/create-project.png" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 

要使用 Azure DevOps 各項功能之前，首先得先建立一個專案，同時必須選擇專案的版本控制是 **TFVC** (Team Fundation Version Control) 還是 **git**，以及專案要使用的流程範本 (Process template)，預設有 **Basic / Agile / Scrum / CMMI** 四種，這次我們就不透過網頁介面建立專案，直接使用 API 小試身手 :fishing_pole_and_fish: 。 

<br>

<!-- more -->

<br>

## 前言

當我們想要使用 **Azure DevOps REST API** 進行程式串接時，首先要確認使用情境是 **`雲端`** 還是 **`地端`**，因為兩者對於 API 的支援程度不盡相同，例如：**Audit log** 目前只有在雲端才能查詢、下載，地端還沒有支援，使用哪一版的 API 也要稍微留意(在[上一篇文章](/post/2024/09/azure-devops-rest-api)有說明原因)，這些都可以在 API 說明頁面左上角透過下拉選單來快速切換檢視，以便我們確認 API 規格與差異。

{% asset_img version_dropdown.png %}

<br>

<div class="tips">
<font color="black">
<a href="https://learn.microsoft.com/en-us/rest/api/azure/devops/?view=azure-devops-rest-7.2" target="_blank">⁍ Azure DevOps Services REST API Reference</a>
</font>
</div>

<br>

## API 規格確認

經由上面的參考連結，我們可以在 **Core > Projects > Create** 找到建立專案的 API 規格：

```HTTP
雲端
POST https://dev.azure.com/{organization}/_apis/projects?api-version=7.2-preview.4
```
```HTTP
地端
POST https://{instance}/{collection}/_apis/projects?api-version=7.0
```

{% asset_img core_projects_create.png %}

<br>

在頁面上對於 Request Body 的屬性和型別宣告都會逐一說明，但唯一美中不足的是我們看不出來哪些屬性是必要項目(Required)，比較簡便的方式是可以使用 Postman 填入 PAT 驗證後進行測試，通常在 Reponse 都會告訴我們缺少什麼必要欄位值，Postman 設定步驟如下：

<br>

1. Authorization 的 Auth Type 選擇 **`Basic Auth`**，並將 PAT 填入 Password 欄位，Username 空白即可。(如何建立 PAT 可以參考：[上一篇文章](/post/2024/09/azure-devops-rest-api))
{% asset_img basic_auth.png %}

<br>

2. Headers 的 Content-Type 設定 **`application/json`**。
{% asset_img content_type.png %}

<br>

3. Body 格式選擇 **`raw`** 和 **`JSON`**，內容先輸入 **`{}`** 然後按下 **`Send`** 按鈕。
{% asset_img 400_bad.png %}

<br>

由 message 回傳的資訊我們就可以得知，必要的屬性有：
- **name** : 專案名稱
- **description**: 專案描述說明
- **visibility** : 專案可層級 (Public或Private，預設是Private)
- **capabilities** : 版控方式、流程範本是必要項目。

完整的 Request Body 內容可以參考下列內容：

```JSON
{
  "name": "MyNewProject",
  "description": "This is a new project created via API",
  "capabilities": {
    "versioncontrol": {
      "sourceControlType": "Git"
    },
    "processTemplate": {
      "templateTypeId": "6b724908-ef14-45cf-84f8-768b5384da45"
    }
  }
}
```

<br>

送出 Request 成功後，可以得到如下訊息：
{% asset_img 202_accepted.png %}

<br>

開啟 Azure DevOps 畫面確認新增的專案是否有正常出現。
{% asset_img my_new_project.png %}

## 流程範本 (Process template)

在 Azure DevOps 預設有 **Basic / Agile / Scrum / CMMI** 四種流程範本，一般我本幾乎不會去關心它的 templateTypeId (GUID) 是什麼，這邊需要透過另一支查詢 Process template 列表的 API 來取得資訊：

```HTTP
雲端
GET GET https://dev.azure.com/{organization}/_apis/process/processes?api-version=7.2-preview.1
```
```HTTP
地端
GET https://{instance}/{collection}/_apis/process/processes?api-version=7.0
```
```JSON
{
    "count": 4,
    "value": [
        {
            "id": "adcc42ab-9882-485e-a3ed-7678f01f66bc",
            "description": "This template is flexible and will work great for most teams using Agile planning methods, including those practicing Scrum.",
            "isDefault": false,
            "type": "system",
            "url": "http://devopssrv/DefaultCollection/_apis/process/processes/adcc42ab-9882-485e-a3ed-7678f01f66bc",
            "name": "Agile"
        },
        {
            "id": "27450541-8e31-4150-9947-dc59f998fc01",
            "description": "This template is for more formal projects requiring a framework for process improvement and an auditable record of decisions.",
            "isDefault": false,
            "type": "system",
            "url": "http://devopssrv/DefaultCollection/_apis/process/processes/27450541-8e31-4150-9947-dc59f998fc01",
            "name": "CMMI"
        },
        {
            "id": "b8a3a935-7e91-48b8-a94c-606d37c3e9f2",
            "description": "This template is flexible for any process and great for teams getting started with Azure DevOps.",
            "isDefault": true,
            "type": "system",
            "url": "http://devopssrv/DefaultCollection/_apis/process/processes/b8a3a935-7e91-48b8-a94c-606d37c3e9f2",
            "name": "Basic"
        },
        {
            "id": "6b724908-ef14-45cf-84f8-768b5384da45",
            "description": "This template is for teams who follow the Scrum framework.",
            "isDefault": false,
            "type": "system",
            "url": "http://devopssrv/DefaultCollection/_apis/process/processes/6b724908-ef14-45cf-84f8-768b5384da45",
            "name": "Scrum"
        }
    ]
}
```

## 後記

再回過頭來看，其實我們上面做的這些動作就是以 API 的方式送出畫面上建立專案的必要欄位而已：

{% asset_img new_project.png %}

<br>

附帶一提，若是以 C# 實作，在 Header 傳遞 PAT 需要先以 **Base64** 編碼才行，可以參考下列完整範例呼叫範例(第31行)：
```CSharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;

class Program
{
	public class ProcessTemplate
	{
		public string Id { get; set; }
		public string Name { get; set; }
	}

	public class ProcessTemplatesResponse
	{
		public List<ProcessTemplate> Value { get; set; }
	}

	static async Task Main(string[] args)
	{
		string pat = "...";
		string serverName = "...";
		string collectionName = "...";
		string url = $"http://{serverName}/{collectionName}/_apis/process/processes?api-version=7.0";

		using (HttpClient client = new HttpClient())
		{
			client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", Convert.ToBase64String(Encoding.ASCII.GetBytes($":{pat}")));
			HttpResponseMessage response = await client.GetAsync(url);

			if (response.IsSuccessStatusCode)
			{
				string result = await response.Content.ReadAsStringAsync();
				var processTemplates = JsonConvert.DeserializeObject<ProcessTemplatesResponse>(result);

				foreach (var process in processTemplates.Value)
				{
					Console.WriteLine($"Name: {process.Name}, ID: {process.Id}");
				}
			}
			else
			{
				Console.WriteLine($"Failed to get process templates: {response.StatusCode}");
				string error = await response.Content.ReadAsStringAsync();
				Console.WriteLine(error);
			}
		}
	}
}
```

<br>

<div class="tips">
<font color="black">
<a href="https://learn.microsoft.com/zh-tw/azure/devops/integrate/how-to/call-rest-api?view=azure-devops" target="_blank">⁍ 開始使用 REST API
</a>
</font>
</div>