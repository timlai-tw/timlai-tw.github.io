---
title: .NET Conf 2025 - 碼農出擊 ! GitHub Copilot 帶你飛
date: 2025-12-14 12:20:09
tags:
 - .NET Conf 2025
 - GitHub Copilot
sticky: 100
---

<img src="/post/2025/12/dotnet-conf-2025/dotNet-conf-2025.jpg" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 

很開心受邀在 **.NET Conf 2025 Taiwan** 擔任講者，這場活動由 STUDY4 社群主辦，是全球 .NET Conf 的台灣在地場，今年主軸放在 **.NET 10** 與 **AI 驅動的應用開發**。我的議程主要聚焦在：當企業想要導入 GitHub Copilot，不只是單純「幫工程師補程式碼」，而是思考如何有計畫地將 GitHub Copilot 融入 SDLC 既有流程之中。<br>

<!-- more -->

<br>
<br>
{% asset_img presentation.jpg %}
<br>

## 從 DevDays Asia 2025 延伸而來

9月我在 **DevDays Asia 2025** 分享的是「Azure DevOps 與 GitHub Copilot：AI 驅動的團隊協作新時代」，比較像是從高空俯瞰：

- 怎麼用 Azure DevOps + GitHub Copilot 重新包裝 SDLC
- 就算還在 On-Premises 或混合環境，也一樣可以享受 AI 輔助開發
- 讓 LLM 透過 Tools / MCP 介接既有系統，把「會聊天」變成「會做事」

這次在 .NET Conf 2025，則是把鏡頭拉近到 **「GitHub Copilot 本身該怎麼在企業裡落地」**，特別是：

1. 從哪幾個層次開始導入比較務實？
2. 怎麼跟 SDLC 的每一個階段對得上？
3. 除了 IDE 內的 Chat / Completions 之外，團隊層級還能玩到多深？

<br>

## 四層次的 GitHub Copilot 客製化思維

簡報裡我用了一個「**AI 客製化四層次架構**」來整理 Copilot 在企業導入時可以怎麼分階段思考：

<br>
{% asset_img levels.jpg %}
<br>

1. **模型與提示詞（Models & Prompting）**  
	先搞清楚目前 Copilot 內建提供哪些模型、哪些是 0x 乘數（不額外計費）、哪些是進階付費模型，依照團隊的情境選擇適合的組合，再搭配基本的 Prompt 寫法，讓開發者在 Chat / Inline Chat 裡可以比較穩定地拿到想要的答案。

2. **自訂指令與提示詞（Custom Instructions & Prompts）**  
	把「每次都會重複說的那些話」抽出來，例如：命名規則、例外處理策略、錯誤訊息語氣、專案的架構慣例等，寫進：
	- **`/.github/copilot-instructions.md`**（工作區全域規則）
	- **`/.github/instructions/*.instructions.md`**（依語言 / 任務做條件式套用）  
	讓 Copilot 自動帶著這些背景知識工作，而不是每次對話都從零開始重新交代。
    - **`/.github/prompts/*.prompt.md`** (會重複使用的 prompt)

3. **自訂代理人（Custom Agents）**  
	把「角色」概念正式化，為不同開發階段建立專屬 Agent，例如：
	- 專門負責需求拆解與規劃的 PLANNER
	- 專心寫程式碼與重構的 IMPLEMENTER
	- 聚焦測試與驗證的 TESTER
	- 做 Code Review 與安全檢查的 REVIEWER  
	然後用 **`/agents/*.agent.md`** 定義好他們各自的行為模式與可用工具，讓團隊在 Copilot Chat 或 Copilot CLI 裡可以一鍵切換角色工作。

4. **整合應用（Integration）**  
	最後一層就是把 Copilot 拉進既有的工具鏈：
	- 在 CI/CD Pipeline 裡呼叫 Copilot CLI 做自動化 Code Review 或文件生成
	- 搭配 Pull Request Flow，讓 Copilot 先幫忙整理變更摘要、找出可疑區塊
	- 用 Prompt Files 把常用工作（例如：「幫我重寫這份 README」）標準化，讓團隊成員都能重複使用同一套流程

這四個層次並不是一次到位，而是可以循序漸進、視團隊成熟度慢慢往上疊。

<br>

## GitHub Copilot 與 SDLC 的結合

在 SDLC 的觀點，我會把 Copilot 放進這樣一個簡化版流程裡看（更多的應用可以參考前幾天提到的 <https://sdlc.timlai.dev>）：

```text
Plan (規劃)
  ↓
  └─ Copilot Chat：輔助需求釐清、工作拆解、規格草稿

Code (程式編寫)
  ↓
  ├─ Code Completions：自動補全樣板程式碼與重複片段
  └─ Copilot Chat：在 IDE 裡協助除錯、重構、產生單元測試

Review (程式碼審查)
  ↓
  └─ Copilot CLI / Agents：自動化 Code Review、風險掃描與文件補強

Deploy (部署)
  ↓
  └─ Coding Agent / Pipeline 整合：協助修改設定、產生部署腳本、建立 PR
```

對企業來說，重點不是「多一個會寫程式的 AI」，而是：

- 在每一個階段都有明確、可度量的應用場景
- 知道哪些事情適合交給 Copilot，哪些仍然要由人類工程師把關
- 把規則寫進 Custom Instructions、流程寫進 Prompt Files 或 Agents，讓團隊可以**穩定地複製成功經驗**

<br>

## 從個人到團隊的導入建議

簡報裡我也分享了一個比較務實的導入路線，給正在觀望是否要導入 Copilot 的團隊參考：

1. **先從開發者個人開始體驗**  
	讓團隊成員先在 VS Code / Visual Studio 裡熟悉 Chat、Completions、Inline Chat 等基本能力，累積最直覺的「效率感」。

2. **很快地補上團隊層級的規範**  
	不要讓每個人用 Copilot 的方式完全長得不一樣，建議盡早建立：
	- 共同的 **`copilot-instructions.md`**
	- 基本的安全與隱私原則（哪些程式碼 / 資料不能貼上雲端）
	- 簡單的「好 Prompt 範例庫」

3. **針對關鍵流程做深度整合**  
	找出團隊最痛的幾個流程（例如：Code Review 太花時間、文件總是寫不完、重複的樣板專案很難維護），優先用 Copilot CLI、Custom Agents 或 CI/CD 整合來做自動化，讓大家看到「不只是省幾行程式碼」的價值。

4. **持續迭代與量化成果**  
	導入一段時間後，回頭檢視：
	- PR 週期、缺陷率、上線前修正量是否有改善？
	- 開發者的主觀感受是「變快」還是「變亂」？
	- 有沒有新的反模式需要在指令或流程上修正？

這次的簡報放在下方參考連結，也非常歡迎有參加活動、或正在思考 **GitHub Copilot 如何在自己團隊落地** 的朋友留言交流，一起把 AI 真正變成開發流程的一部分，而不是只停留在「玩玩看」的階段。

<div class="tips">
<font color="black">
<a href="/post/2025/12/dotnet-conf-2025/碼農出擊 ! GitHub Copilot 帶你飛_20251129.pdf" target="_blank">⁍ 簡報下載 - 碼農出擊 ! GitHub Copilot 帶你飛</a><br>
<a href="https://dotnetconf.study4.tw" target="_blank">⁍ .NET Conf 2025 Taiwan 官方網站</a>
</font>
</div>