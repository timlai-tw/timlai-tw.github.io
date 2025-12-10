title: Agentic AI 與 SDLC 協奏曲
date: 2025-12-09 21:42:51
tags:
 - Agent AI
 - Agentic AI
 - SDLC
---

<img src="/post/2025/12/agentic-sdlc-explorer/sdlc.jpg" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 

在工作上我經常需要跟客戶討論 DevOps 與軟體工作流程，從需求管理、版本控管，到建置、測試與佈署，這幾年又加上 AI / Agentic AI 的導入議題，光靠投影片或架構圖常常很難在短時間內讓大家有「同一個畫面」。也因為這樣，我做了一個小小的 side project —— **Agentic SDLC Explorer**，想用一個可以點點看的互動式網站，來幫助大家快速了解 Agent 在 SDLC 各階段可以扮演什麼角色。

<!-- more -->

<br>

## 為什麼會做 Agentic SDLC Explorer？

這幾年在跟客戶討論 DevOps 或 SDLC 優化時，越來越常被問到幾個問題：

- 「AI 在我們的開發流程裡，**到底能幫上什麼忙**？」
- 「除了 IDE 裡的 GitHub Copilot，能不能有更『流程化』的應用？」
- 「如果要導入 Agent，是不是整個 SDLC 都要重改？」

多數團隊其實已經有一套既有流程（Jira / Azure DevOps / GitHub / Jenkins / 自家 Pipeline…），真正的問題不是「要不要用 AI」，而是：

- 哪些工作可以交給 Agent？
- 這些 Agent 應該長什麼樣子？
- 在實務中，怎麼跟既有 SDLC 串在一起？

所以我做了這個 **Agentic SDLC Explorer**，把整個 SDLC 拆成幾個大家比較熟悉的階段，試著用「**如果有一個 Agent 在這裡，它可以幫你做哪些事？**」這個角度來整理，方便在顧問或工作坊場合用來示範與討論。

<br>

## 什麼是 Agentic SDLC Explorer？

簡單來說，**Agentic SDLC Explorer** 是一個以 **Vite + React** 打造的輕量前端示範專案，用來視覺化展示 Agent（自動化代理）在軟體開發生命週期（SDLC）中可以協助的各種工作。畫面上會把 SDLC 的不同階段切成數個區塊，點選每個區塊可以展開對應的 Agent 情境與說明，讓大家不用看長篇文件，就能快速瀏覽可能的應用方式。

這個專案目前主要聚焦在「**情境與概念整理**」，也就是：

1. 幫你把腦中鬆散的想法具體化：哪些工作其實可以交給 Agent？
2. 提供一個討論框架：在跟團隊或利害關係人溝通時，有一個共同的視覺化藍圖可以對焦。

<br>

## 目標與設計理念

在設計這個 side project 時，我自己抓了兩個核心目標：

1. **示範 Agent 在不同 SDLC 階段的可能應用**  
	 從需求分析、系統設計、開發、測試、安全、程式碼審查，到部署與維運，嘗試列出各階段可以交給 Agent 處理或輔助的任務，讓大家看到「不只是在寫程式時可以用 GitHub Copilot」。

2. **提供可直接執行的範例介面**  
	 雖然現在版本主要還是概念展示，但整個專案結構就是一般前端專案，日後可以很容易地把某些情境換成真實 API / Agent 後端實作，慢慢演進成一個「可以真的點一點就幫你做事」的 Agentic SDLC 工具箱。

<br>

## Agent AI 在 SDLC 中的應用範疇

在 Agentic SDLC Explorer 裡，我把 SDLC 粗略切成幾個比較容易對應到日常工作的區塊，下面逐一說明每個範疇適合出現哪些 Agent，以及它們可以幫上什麼忙。

<br>

### 1. 需求 / Backlog：從想法到可實作的工作項目

在需求管理這一段，我通常會想像有幾種類型的 Agent：

- **需求整理 Agent**：
	- 協助整理會議紀錄、Email、聊天訊息，把零散的想法歸納成可管理的 Backlog 項目。
	- 協助補上缺少的欄位，例如：商業價值、風險、驗收條件、相依關係等。

- **規格檢視 Agent**：
	- 檢查需求描述是否含糊不清、是否缺少邊界條件或錯誤情境。
	- 參考現有系統文件或程式碼，提醒「這個需求可能會影響到哪些模組」。

- **系統設計輔助 Agent**：
	- 根據需求內容，協助產生初步架構圖、資料流、模組切分建議。
	- 提出多種實作選項（例如：同步 vs 非同步、事件式設計 vs 傳統呼叫），幫助架構師做決策。

在 Explorer 的介面上，這一塊會被標示成「需求 / Backlog」，點進去可以看到上面提到的這類情境，方便跟 PM 或 BA 討論「哪些還是要人決定、哪些可以交給 Agent 做第一輪整理」。

<br>

### 2. 開發 / Agentic Coding：讓 IDE 真的變成你的 Pair

談到開發階段，大家第一個想到的通常是 IDE 裡的 GitHub Copilot 或類似工具，但在 Agent 視角下，可以再往前幾步：

- **程式碼理解 Agent**：
	- 對既有程式碼庫進行分析，協助回答「這支服務在做什麼？」「這段 legacy code 改動會影響哪裡？」。
	- 依需求或 Issue，自動找出相關檔案、類別與測試，當成開發起手式。

- **多語言程式碼翻譯 Agent**：
	- 把 VB6 / VB.NET / Java 等舊系統片段，逐步翻譯成目標語言（例如 C#、TypeScript），同時標註需要人工確認的區塊。

- **Refactoring / Pattern 建議 Agent**：
	- 觀察重複邏輯，提出重構建議（提煉函數、抽共用模組、導入設計模式等）。
	- 在 PR 或本地編輯階段，提醒程式碼味道（Code Smell）或潛在技術債。

這些都不一定要是「一個超級大 Agent」，實務上更常見的做法是：

- 讓 IDE 內建 GitHub Copilot 負責即時補全與解說，
- 再搭配幾個針對特定語言或框架的後端 Agent，負責比較重的分析工作，
- 透過 MCP / API 或自訂工具，把結果回寫到開發者的工作環境中。

<br>

### 3. 測試與安全：從「寫測試」變成「設計測試策略」

在測試這一段，Agent 很適合扮演的是「測試策略設計師」與「測試產生器」：

- **單元測試產生 Agent**：
	- 根據既有程式碼與需求描述，自動產生單元測試範本，並幫你補齊常見的正常 / 例外情境。
	- 協助重構既有測試，讓測試名稱更清楚、結構更一致。

- **整合測試 / 合約測試 Agent**：
	- 根據 API 規格（OpenAPI / gRPC / 內部文件），自動產生合約測試樣本與測試資料集。
	- 協助模擬下游系統行為（Mock / Stub），加速測試環境就緒。

- **安全性分析 Agent**：
	- 包裝 SAST / DAST 工具的輸出結果，轉成比較「人看得懂」的風險說明與修正建議。
	- 在 PR 或 Build 階段對惡意輸入、權限控管、密碼與金鑰管理作靜態檢查，幫忙標註優先順序。

當然，最終的測試策略仍需要測試工程師或團隊自己決定，但 Agent 可以把大量「機械式、可重複」的工作處理掉，讓人類把時間花在設計更好的測試場景，而不是在那邊苦苦寫樣板。

<br>

### 4. 程式碼提交與審查：讓 PR 審查更聚焦在設計與風險

在 Commit / PR 流程中，可以想像幾個常見的 Agent：

- **變更摘要 Agent**：
	- 針對這次 Commit / PR，自動整理「改了什麼」、「為什麼要改」、「可能影響到什麼」三個重點。
	- 協助產生較高品質的 PR 描述，讓 Reviewer 不用自己從 diff 猜動機。

- **Code Review 佐助 Agent**：
	- 結合程式碼規範（Coding Guidelines）、安全規則與團隊慣例，先跑一輪初步檢查，把明顯問題先標出來。
	- 幫 Reviewer 統整重點：哪幾個檔案改最多？哪幾個區塊需要多人共同確認？

- **合規 / 安全審查 Agent**：
	- 對涉入隱私資料、金流、權限的變更多做一層檢查，確保沒有誤用敏感 API 或外洩機會。

這類 Agent 不一定要「自動幫你按 Approve」，比較實際的做法是：

- 把 Review 的「雜訊」濾掉，
- 協助排出「這個 PR 裡最值得你花時間看的地方」，
- 讓 Reviewer 有更多時間專注在架構設計、風險與長期維護性。

<br>

### 5. 部署與維運：從 Log 與警報中找出可以行動的訊息

在 CI/CD 與維運階段，Agent 的角色通常比較偏向「觀察者 + 分析師」：

- **Pipeline 輔助 Agent**：
	- 監看建置結果與測試報告，將錯誤訊息轉成具體建議（例如：哪個測試常壞？是 flaky 還是真的有問題？）。
	- 針對常失敗的 Stage，提出切分或重構建議，協助 DevOps 工程師優化 Pipeline。

- **Incident / On-call 助理 Agent**：
	- 當監控系統跳出警報時，整理相關的 Log、最近變更、影響的服務與使用者數量，讓 On-call 能在幾分鐘內掌握大局。
	- 協助產生初版的 Incident Report 或 Postmortem 草稿，後續再由團隊補完。

這些應用都很適合被視覺化放在 SDLC Explorer 裡，幫助團隊思考：「我們目前哪一段流程最痛？是不是可以先從這裡開始試驗 Agent？」

<br>

### 6. 文件與發佈：讓知識累積變得更自然

最後一塊是很多團隊都想做、但總是排不出時間的：「文件與發佈」。

- **文件產生 / 更新 Agent**：
	- 根據需求、設計決策與程式碼變更，自動產生或更新 API 文件、架構說明、系統操作手冊草稿。
	- 協助比對「實際系統行為」與「文件描述」是否一致，標出可能已過期的段落。

- **Release Notes 助理 Agent**：
	- 根據這個 Sprint 或 Release 所有 PR / Work Items，整理出對使用者友善的更新說明。
	- 針對不同受眾（End User / 內部技術人員 / 管理層）產生不同版本的摘要。

- **溝通與訓練素材 Agent**：
	- 把新功能的設計說明轉成簡報、教學稿、FAQ，減少重複解釋的時間。

這些工作本質上都很適合由 Agent 先做 70%，再由人類補上最後 30% 的細緻度與裁量。

<br>

## Explorer 本身可以怎麼擴充？

目前的 Agentic SDLC Explorer 主要還是「情境導覽 + 視覺化地圖」，但因為是走 **Vite + React 的標準前端專案結構**，未來可以有很多延伸方向，例如：

- 在每個 SDLC 區塊下，掛上真實後端 Agent 的 API，讓使用者可以直接在畫面上觸發 Demo（例如：產生測試、總結 Issue、分析 Log）。
- 加入篩選與標籤功能，依角色（開發 / 測試 / DevOps / PM）或成熟度（PoC / Pilot / Production）來瀏覽適合的情境。
- 把成功案例與實作筆記寫回到這個網站，形成一種「Agentic SDLC 實戰索引」。

如果你有其他想補充的場景，也非常歡迎直接發 PR 到 GitHub 專案，一起把這張 SDLC + Agent 地圖補得更完整。

<br>

## 結語

Agent AI 帶來的不是「一個超強的萬能助理」，而是讓我們可以在 SDLC 的各個角落，放進許多專精且可協作的 Agent，幫忙處理大量重複、機械、需要跨系統查資料的工作。**Agentic SDLC Explorer** 這個 side project，只是我在顧問工作中為了方便說明而做的一個小工具，但也希望它能成為大家在規劃 AI / Agent 導入時的一個起點：

- 先看清楚目前 SDLC 上有哪些痛點與瓶頸。
- 想像如果有一個 Agent 待在那個位置，它可以幫你做什麼。
- 從最有感、風險最低的一兩個情境開始試驗。

Demo Site 我已經發佈到 https://sdlc.timlai.dev/ ，大家也可以直接到 GitHub 專案 https://github.com/timlai-tw/agentic-ai-sdlc 提 issue、分享你在團隊裡導入 Agentic SDLC 的經驗，我也會持續在這個部落格記錄相關的實作心得與工具觀察。

<div class="tips">
<font color="black">
<a href="https://sdlc.timlai.dev" target="_blank">⁍ Agentic AI and SDLC Orchestration</a><br>
<a href="https://github.com/timlai-tw/agentic-ai-sdlc" target="_blank">⁍ GitHub | Agentic SDLC Explorer</a>
</font>
</div>

