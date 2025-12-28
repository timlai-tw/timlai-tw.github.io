---
title: Azure DevOps Server 隨附的 Elasticsearch 啟動問題
date: 2025-12-26 18:18:44
tags:
 - Azure DevOps
 - Elasticsearch
 - Code Search
---

<img src="/post/2025/12/ado-elasticsearch-814/title.jpg" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/>

Azure DevOps Server 的 **Code Search** 功能，對於大型 repo 的日常使用上很有幫助：我們可以用關鍵字快速跨專案/跨 repo 搜尋類別、方法、字串常數、甚至是正規表示式；搭配權限控管，團隊就能在同一平台裡完成程式碼查詢流程。但也因為 Code Search 背後依賴 Elasticsearch，**地端的 Azure DevOps Server** 要啟用 Code Search，就需要自行安裝「產品隨附」的 Elasticsearch 版本。本篇整理一個在升級/重裝時很容易遇到的小坑：明明安裝完成了，但 Elasticsearch 8.14.1 Windows Service 卻無法正常啟動。

<!-- more -->

<br>

## Code Search 做什麼？
Code Search 主要解決兩件事：

- **快速定位程式碼**：比起用檔案總管或單純全文檢索，Code Search 對「程式語言結構」更友善，查起來更像在 IDE 裡做全域搜尋。
- **讓知識可被重用**：新同事接手、查 bug、追一段歷史變更，很多時候第一步就是「先找到相關程式碼」，Code Search 可以協助我們讓所有事情更加順暢。

## Code Search 還能全文檢索哪些 Azure DevOps 資料？
除了程式碼本身，Azure DevOps 的搜尋（Code Search / Search）在特定版本與設定下，也能對平台內的其他文字內容做全文檢索，常見包含：

- **工作項目（Work Items）**：標題、描述與評論中的文字。
- **Pull Request**：標題、描述與留言（comments）。
- **Commit / 提交訊息**：提交訊息與相關變更說明。
- **Wiki 頁面**：頁面標題與內容。
- **儲存庫相關文字**：檔案內容、註解、以及某些情況下的分支或標籤名稱。
- **其他（視設定）**：像是附件內的可索引文字或管線/設定檔中的文字，是否納入索引會依安裝版本與索引設定而異。

實際可檢索的項目會受 Azure DevOps 的版本、安裝的擴充套件與搜尋索引設定影響，可參考下列官方文件說明。

<div class="tips">
<font color="black">
<a href="https://learn.microsoft.com/zh-tw/azure/devops/project/search/get-started-search?view=azure-devops-server" target="_blank">⁍ 開始使用搜尋功能</a><br>
<a href="https://learn.microsoft.com/zh-tw/azure/devops/project/search/functional-code-search?view=azure-devops-server" target="_blank">⁍ 功能代碼搜尋</a><br>
<a href="https://learn.microsoft.com/zh-tw/azure/devops/project/search/functional-work-item-search?view=azure-devops-server" target="_blank">⁍ 功能性工作項目搜索</a><br>
<a href="https://learn.microsoft.com/zh-tw/azure/devops/project/search/install-configure-search?view=azure-devops-server" target="_blank">⁍ 安裝和設定搜尋</a><br>
<a href="https://learn.microsoft.com/zh-tw/azure/devops/project/search/manage-search?view=azure-devops-server" target="_blank">⁍ 管理搜尋索引編製</a>
</font>
</div>

## Azure DevOps Server 的 Code Search 為什麼要自己裝 Elasticsearch？
在 Azure DevOps Services (雲端) 我們不需要考慮 Elasticsearch 安裝與設定的問題，但是在 Azure DevOps Server (地端) 情境，Code Search 依賴的搜尋引擎就得自己進行部署設定，安裝檔案一般會隨著安裝後的 Azure DevOps Server 放在下列路徑：

```text
C:\Program Files\{TFS Version Folder}\Search\zip
```

不同版本的 Azure DevOps Server 隨附的 Elasticsearch 版號也不同，最近幾年的三個版本如下：

| Azure DevOps Server 版本 | 隨附 Elasticsearch 版本 |
| --- | --- |
| Azure DevOps Server 2020 | Elasticsearch 6.2 |
| Azure DevOps Server 2022 | Elasticsearch 7.17 |
| Azure DevOps Server | Elasticsearch 8.14 |

## 問題：服務無法啟動 (Error 1067)
在全新的環境安裝 Azure DevOps Server 隨附的 Elasticsearch 8.14，通常不會有任何問題。

但如果同一台電腦 **曾經安裝過較早版本** 的 Azure DevOps Server 隨附 Elasticsearch（例如：6.2 或 7.17），即使順利的把 8.14 安裝完成，也可能在啟動 Windows Service 時遇到下列錯誤：

{% asset_img 01.jpg %}

錯誤訊息：

```text
Windows could not start the Elasticsearch ... service on Local Computer.
Error 1067: The process terminated unexpectedly.
```

## 從服務啟動命令看端倪：procrun 的 //RS//
我們可以在服務的內容找到實際執行的命令：

```text
elasticsearch-service-x64.exe //RS//elasticsearch-service-x64
```

{% asset_img 02.jpg %}

這串其實是 Apache Commons Daemon（procrun）的標準語法，其中：

- **`//RS//`** 不是一般參數，而是 **動作代碼 (action code)**：表示「以 service 模式執行，並從 registry 讀取設定」。
- elasticsearch-service-x64.exe 本質上就是 procrun 的 prunsrv.exe（只是被 Elastic 改名），在 Windows 上扮演 Service wrapper，負責：
	- 向 Windows Service Control Manager 註冊/啟動/停止服務
	- 啟動 JVM，載入 Elasticsearch 的 main class
	- 轉接 stdout/stderr 到 log

procrun 常見的 `//XX//` 動作代碼如下（只列這次會用到的）：

- **`//IS//`**：Install Service（安裝服務）
- **`//DS//`**：Delete Service（刪除服務）
- **`//TS//`**：Run as console（測試模式，在前景跑）
- **`//RS//`**：Run Service（以服務模式啟動，從登錄載入所有設定）

## 用 //TS// 前景啟動抓錯誤
既然 **`//RS//`** 代表「讀取 registry 後以服務模式跑」，我們就可以改用 **`//TS//`** 前景啟動，把錯誤訊息印出來：

```bat
elasticsearch-service-x64.exe //TS//elasticsearch-service-x64
```

{% asset_img 03.jpg %}

在圖中的啟動過程，可以看到帶入了 **--quiet** 參數，並且出現：

```text
ERROR: quiet is not a recognized option
```

經過反覆測試可以確認：

- **全新環境** 安裝 Elasticsearch 8.14 時，正常情況下不會多帶這個參數。
- ==但如果同機 **曾安裝較早版本** 的 Azure DevOps 隨附 Elasticsearch，8.14.1 安裝後的服務設定可能會延續/殘留某些舊設定，讓啟動時多帶了 **--quiet** 而導致服務啟動異常結束。==

## 解法：用 elasticsearch-service.bat manager 移除 --quiet
解法其實很單純：用管理介面把 Startup 參數裡的 `--quiet` 刪掉。

在 Elasticsearch 安裝目錄的 `bin` 下執行：

```bat
elasticsearch-service.bat manager
```

接著在 Startup 分頁，把紅框中的 **--quiet** 刪除（如下圖）：

{% asset_img 04.jpg %}

套用後重新啟動服務，就就可以正常將服務順利啟動。

## 補充：Java Heap (Xms / Xmx) 記得人工確認
在剛才的設定截圖中，還有一個 Java 頁籤，它是對應到 JVM 的 heap 設定：

- **Initial memory pool**：**`Xms`**
- **Maximum memory pool**：**`Xmx`**

{% asset_img 05.jpg %}

這兩個值「合理」與否，會跟實體記憶體、索引規模、查詢負載都有關。比較通用的原則如下：

- **`Xms`** **不超過實體記憶體的 50%**（還要留給 OS 與 filesystem cache）。
- **`Xmx`** **最好不要超過約 32GB**（超過可能失去 compressed oops 的效益，反而變慢）。
- 通常建議 **Xms = Xmx**，避免 JVM 在執行期間動態調整 heap 尺寸造成額外負擔。

舉例（單機單節點、且同機沒有其他吃記憶體的大服務時）：

- 16GB RAM：可設 4–8GB（例如：4096MB 或 6144MB）
- 32GB RAM：可設 8–16GB（例如：8192MB 或 12288MB）

==另外要注意：如果日後伺服器記憶體有異動，這個數值 **不會自動跟著調整**，記得把「確認/調整 Elasticsearch heap」列入升級與維運的 checklist。==


