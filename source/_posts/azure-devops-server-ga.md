---
title: Azure DevOps Server 正式版發佈
date: 2025-12-12 09:00:00
tags:
  - Azure DevOps
---

<img src="/post/2025/12/azure-devops-server-ga/ga.jpg" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/>

微軟在 2025/12/9 宣佈 Azure DevOps Server 已正式達到 GA（General Availability，正式版）狀態，這次 RTW 版本是針對企業自架環境的一次重要里程碑：除了支援 SQL Server 2025、整合 RC 階段已釋出的功能外，也彙整了多項在 Boards、Repos、Pipelines、Test Plans、Reporting、Wiki 等服務上的改進。本文說明為什麼名稱不再掛版號、GA 的發佈重點，以及各服務的新功能亮點，方便大家評估升級與導入時機 :blush:。

<!-- more -->

<br>
<br>
{% asset_img azure-devops-server-ga.jpg %}
<br>

## 為什麼 Azure DevOps Server 不再加上版號？

如果大家最近有留意微軟官方文件，會發現標題多半只有「Azure DevOps Server」，而不是像以前那種「Azure DevOps Server 2022 / 2020」清楚掛版號的寫法，主要可以從幾個方向來理解：

- **名稱回到「產品」本身，而不是單一年度版本**  
  微軟希望把 Azure DevOps Server 視為一個持續演進的產品，而不是每隔幾年就出一個「完全不同」的新名字。實際上底層還是有 build number 與發行批次，只是對使用者來說，重點放在「最新 Azure DevOps Server + 對應累積更新」；就像我常被客戶問到：今年都 2025 年了，Azure DevOps Server 最新版還在 2022.2，微軟是不是很久沒更新這個產品了？~~(大誤)~~
- **以「發行批次 / 累積更新」與支援週期為主軸**  
  官方的 Release Notes 也改成「Azure DevOps Server Release Notes」這樣的形式，新的 RTW/RC、累積更新（cumulative updates）都掛在同一頁，讓大家用「目前安裝的是哪一個 release」來辨識，而不是只記年份或版號。
- **與雲端 Azure DevOps Services 的節奏對齊**  
  雲端服務本來就是持續更新、不強調版號，地端版也朝這個方向靠攏。對於有混合情境（雲端 + 地端）的團隊來說，在比較功能差異或規劃遷移時也比較直覺。

換句話說：版本仍然存在（build number、支援週期一樣重要），只是公開溝通時不再把「年份」放在產品名稱裡，而是強調「這一個 Azure DevOps Server 發行批次」所帶來的變化與支援範圍。

<br>

## 正式版發佈重點（GA / RTW Highlights）

根據官方部落格與 Release Notes，這次 Azure DevOps Server RTW 主要有幾個值得注意的重點：

- **從 RC 過渡到正式版，適合生產環境全面部署**  
  這次 GA 是在 RC 基礎上進一步收斂修正後的 RTW 版本，針對企業實際使用情境做了額外的穩定度與品質調整，定位上已經是可以在正式環境大規模導入的版本。
- **支援 SQL Server 2025**  
  RTW 明確標示為「支援 SQL Server 2025 的彙總發行（roll up）」；如果大家準備在資料庫層升級到 SQL Server 2025，這個版本就是官方對應的 Azure DevOps Server 搭配選項。
- **直接升級路徑清楚**  
  官方支援從 Azure DevOps Server 2019 / 2020 / 2022，或 Team Foundation Server 2015（含）之後的版本直接升級到 Azure DevOps Server。若還停在 TFS 2013 甚至更早，就需要先做中繼升級（要先升級到 2015 才能再升到目前最新的 Azure DevOps Server）。  
  ==同時也提醒：TFS 2015 已在 2025/10/14 結束延伸支援，若還停留在舊版本，這次 GA 其實是很好的「順勢升級」時機。==
- **品質修正與細節優化**  
  RTW 版本除了合併本地化調整外，還修正了多個實務上會遇到的問題，例如：
  - 儀表板 Code Tile 在儲存庫或分支名稱太長時的顯示問題。
  - GitHub PR 狀態透過 webhook 回寫時，已合併與未合併都被記成 Closed 的狀況，現在會依 payload 的 merged flag 正確區分。
  - 某些 Work Item Linking 情境下，Visual Studio 會因遞迴屬性造成當機的問題。
  - Collection 設定頁中，如果 Analytics 被暫停或停用，Agent Pool 頁面會載入失敗的情形，現在已可正常顯示。
- **完整的下載與文件連結**  
  官方提供 ISO、Web Install、下載頁、系統需求/相容性與安裝文件，方便 IT 做標準化佈署與維運規劃。

<br>
{% asset_img upgrade-azure-devops-server.png %}
<br>

## 新功能與改進：各服務重點整理

這次 GA 包含 RC 階段累積的一大串新功能與改進，底層細節很多，這裡依服務簡單抓幾個實務上比較有感的部分做摘要，方便大家快速掌握方向。

### General

- **Markdown 程式碼區塊一鍵複製**  
  Wiki、Repos 檔案預覽、PR 討論與 Work item 討論中的程式碼區塊，都多了一個 **`Copy to clipboard`** 按鈕，貼上範例或文件內容給團隊成員時效率更高。
- **Delivery Plans 權限更細緻**  
  新增「Manage Delivery Plans」專用權限，避免 Readers 類型的使用者不小心取得不必要的寫入權限，有助於大型組織控管誰可以建立或維護計畫視圖。

### Boards

- **GitHub PR 中的 AB# 連結顯示優化**  
  在 PR 描述裡用 AB#123 這種方式連結 Work item 時，現在會直接出現在 GitHub PR 的 Development 區塊，從 PR 就能快速回到對應的 Azure Boards 工作項目，追蹤性更好。
- **連結大量 GitHub Repos 的體驗改善**  
  當一個 GitHub 組織裡有成千上萬個儲存庫時，以前在 Azure Boards 裡搜尋/連結很容易 timeout，現在搜尋與載入流程經過最佳化，大型組織會感受比較明顯。
- **YAML Pipelines 的「Integrated in build」連結支援 GitHub**  
  針對放在 GitHub 的 repo，現在 YAML pipeline 也能在完成 build 後自動在相關 Work item 上顯示「已在哪個 build 中整合」，讓 YAML 與傳統 Classic pipeline 在追蹤能力上更接近。
- **GitHub 連線 REST API**  
  新增一組 API 讓我們可以程式化地查詢、新增或移除 GitHub Repositories 連結，並將單一連線可掛載的 repo 上限從 500 提升到 2,000，方便用 Script 自動化維護大規模整合。
- **刪除 Area / Iteration 更安全**  
  現在只有在沒有任何 Work item 使用該路徑時，才能刪除 Area 或 Iteration，避免一個不小心影響一大堆看板、報表與存取權限。
- **標籤與留言體驗升級**  
  刪除的 Tag 不會再出現在建議清單裡，Work item 討論也能更方便貼上來自 Teams、Email、Word 等來源的圖片，REST API 另外新增留言數量限制，避免濫用導致效能或安全問題。
- **Delivery Plans 數量上限提高**  
  每個專案可建立的 Delivery Plans 上限，從 1,000 提升到 1,500，對於有大量跨團隊規劃需求的組織比較友善。

### Repos

- **可選擇停用新的 TFVC 儲存庫建立**  
  Git 已經是主流，微軟也明確表示近年不再為 TFVC 加新功能。這次新增一個設定，讓我們可以關閉「建立新的 TFVC Repo」，既不影響既有 TFVC 專案，又能避免新專案再誤用 TFVC。
- **Git submodules UI 支援**  
  在檔案總管（Files hub）中，可以直接看懂並點進 submodule 指向的儲存庫與特定 commit，支援 Azure Repos、GitHub、GitLab、Bitbucket 等多種來源與 URL 格式，對有 Monorepo 或模組化拆分的團隊很實用。
- **Repository Health and usage 面板**  
  大型儲存庫會拖慢整體效能，現在 Repos 會監看儲存庫大小、commit 密度與內容結構等指標，必要時會提示 repository owner 檢查健康狀態與最佳化建議。
- **自訂 PR 目標分支建議**  
  只要在預設分支下建立 .azuredevops/pull_request_targets.yml，就可以定義常見的目標分支（例如 main、release/* 等），建立 PR 或系統自動建議 PR 時就會優先採用這些設定，減少開 PR 開錯分支的情況。
- **Markdown 支援 Mermaid 圖表與 PR 標題搜尋**  
  Repos 中的 Markdown 如果包含 mermaid 語法，現在會直接渲染成圖表；PR 列表頁也多了依標題搜尋的功能，方便在大量 PR 中快速找到目標。
- **Pipelines Checkout 支援 sparse checkout**  
  YAML 的 checkout 步驟新增 sparseCheckoutDirectories / sparseCheckoutPatterns 兩個設定，可只抓部分目錄或路徑，大型 repo 跑 CI/CD 時可以明顯縮短 checkout 時間（需要較新的 Agent 版本）。
- **其他 API 與相容性改善**  
  包含 cross-repo policy 預覽改成大小寫敏感（與實際規則一致）、TFVC check-in policy 新格式與 NuGet 套件更新、Repo 資訊 API 增加建立時間欄位、PR 查詢 API 可選擇回傳標籤（labels）等。

### Pipelines

- **TFX 與 Task Node runtime 健康檢查**  
  用 TFX 發佈 extension 時，現在會檢查 Task 是否還在使用已 EOL 的 Node.js 版本（例如 Node.js 10/12/16 等），並給出警告訊息，提醒作者儘早升級。
- **Pipeline 執行時也會對老舊 Node.js runner 發警示**  
  如果某個 Task 仍依賴已不再維護的 Node.js 版本，執行時會在 log 裡出現警告，並提供官方升級指引連結；若暫時無法調整，也可以透過環境變數關閉這個檢查（不過長期來看還是建議盡快升級）。
- **DockerCompose@0 改用 Compose v2 相容模式**  
  Hosted Agent 即將移除 Docker Compose v1，因此 DockerCompose@0 Task 會在沒有 v1 時自動改用 v2 的相容模式；若專案還沒做好 v2 相容性，官方也提供範例腳本，教大家在 Pipeline 裡自行下載 v1 執行檔暫時過渡。
- **使用 Microsoft Entra ID 存取 Azure Service Bus**  
  新版 PublishToAzureServiceBus@2 Task 可以用 Azure Service Connection 搭配 Entra ID（原 Azure AD）存取 Service Bus，支援透過 RBAC 控制權限，比直接塞連線字串安全許多，自訂 server task 也能改用 service connection。

### Test Plans

- **Test & Feedback 擴充功能升級到 Manifest V3**  
  Chrome/Edge 上的 Test & Feedback 延伸模組已改用 Manifest V3，跟上瀏覽器的安全性與效能要求，功能維持不變。
- **Azure Test Runner 與 Chrome 相容性修正**  
  針對最近 Chrome 在 URL 處理上的變更，更新後的 Azure Test Runner 版本已修正啟動失敗的問題。
- **Test Plans 與 Build Pipeline 整合更順**  
  在 Test Plan 裡設定好的 Build 定義與 ID 會自動帶到 Web Runner，不需要每次手動重設，減少測試流程中的行政作業。
- **支援透過 REST API 還原刪除的 Test Plan / Suite**  
  不小心刪錯 Test Plan 或 Suite 時，現在可以用 API 查詢並還原（包含子階層），不用再倚賴客服協助，不過既有的執行結果等 artifact 並不會一併回復。
- **匯出 Test Case 到 XLSX 支援自訂欄位**  
  匯出測試案例時可以帶出自訂欄位，做報表或離線分析比較方便。
- **測試執行體驗優化**  
  包含 Test Plans 目錄支援欄位排序、Web/Desktop Runner 提供「Undo」回復步驟狀態、Publish code coverage v2 Task 支援更多格式與 PR 差異覆蓋率（diff coverage）等。
- **Test Plans 支援 YAML Pipelines**  
  除了 Classic pipeline，現在也可以選擇 YAML pipeline 來執行自動化測試，對已全面轉向 YAML 的團隊非常關鍵。

### Reporting & Wiki

- **Backlog Rollup 資料顯示更清楚**  
  Rollup 欄位會顯示最近一次成功計算的資料與時間戳記，即使 Analytics 略有延遲，也比較不會誤以為是資料遺失。
- **Wiki 貼上 HTML 內容自動轉 Markdown**  
  從瀏覽器、Office、Teams 等地方複製的 HTML 內容（像是清單、表格、圖片、連結等）貼到 Wiki 時，會自動轉成對應的 Markdown 語法，編輯起來順手很多。

<br>

## 什麼時候該考慮升級？

微軟於 2025/12/9 將 Azure DevOps Server 正式宣布為 GA，代表地端版本已足夠穩定可在企業生產環境部署，並帶來多項來自 RC 階段的修正與功能強化（例如更好的 GitHub 整合、Repos 健康檢查、sparse checkout、YAML 與 Test Plans 的整合等），對準備升級到 SQL Server 2025 或仍使用舊版 TFS 的團隊來說是很好的升級時機，但在規劃導入時記得也要留意安裝的最低需求 — Windows Server 2022 與 SQL Server 2022，詳細說明可以參考文末的連結。

綜合來看，這次 Azure DevOps Server GA 有幾個關鍵訊號：

- 若後端環境有計畫升級到 **SQL Server 2025**，這是官方明確對應的 Azure DevOps Server 版本。
- 若還在 **TFS 2015 或更舊版本**，這次 GA 搭配延伸支援結束，是很適合規劃「一次到位」升級的時點。
- 如果團隊已大量使用 **Git + YAML Pipelines + GitHub 整合**，這一波新功能會讓追蹤性與操作體驗更接近雲端版 Azure DevOps Services。

在官方的 Release Notes 頁面中有相關功能的截圖，有需要可以再參考下列參考連結。 :sunglasses:

<div class="tips">
<font color="black">
<a href="https://devblogs.microsoft.com/devops/announcing-azure-devops-server-general-availability" target="_blank">⁍ Announcing Azure DevOps Server General Availability</a><br>
<a href="https://learn.microsoft.com/en-us/azure/devops/server/release-notes/azuredevopsserver?view=azure-devops-server" target="_blank">⁍ Azure DevOps Server Release Notes </a><br>
<a href="https://learn.microsoft.com/en-us/azure/devops/server/requirements?view=azure-devops-server&branch=releasenotes%2FAzureDevOpsServer2020" target="_blank">⁍ Requirements for Azure DevOps on-premises </a>
</font>
</div>
