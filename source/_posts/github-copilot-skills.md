---
title: 建立可重用的 GitHub Copilot Skills
date: 2025-12-21 15:44:28
tags:
 - GitHub Copilot
 - Agent Skills
---

<img src="/post/2025/12/github-copilot-skills/agent-skills.jpg" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 

在 GitHub Copilot 的世界裡，我們常常用「一句話」啟動任務，但真正讓 Agent 能穩定把事情做完的，通常不是那一句話，而是背後那套可重複、可維護、可分享的流程知識。**Agent Skills** 就是在這個背景下出現：它把我們原本散落在 prompt、Wiki、筆記、SOP 的內容，封裝成「模型需要時才載入」的技能模組，讓 Copilot 更像一個真的會做事的隊友，而不是只會回答問題的聊天機器人。本文介紹 Agent Skills 在 VS Code 中的使用方式，以及在 token 與可維護性上的優勢，並對比 Custom Instructions 與 MCP 的差異。

<!-- more -->

<br>
<br>

{% asset_img skills.jpg %}

<br>

## Agent Skills 源起

在 Agent 模式逐漸普及後，大家會遇到一個很現實的問題：
同一個「專案流程」我們可能會一直重複類似的描述，例如：怎麼跑測試、怎麼看 log、怎麼開 PR、怎麼寫 release note、怎麼處理 build failure。

如果每次都靠貼上 prompt 或是把規則塞進 instructions 可能會：
- 容易漏掉關鍵步驟（尤其在多人協作的情況下）。
- 規則越長，越容易干擾模型在其他任務的判斷。
- 每個 chat session 固定把一大段規則塞進上下文，token 成本很高。

Agent Skills 的設計就是為了解決這些痛點：把「可重用的專業流程」抽成一個資料夾（包含：說明、腳本、範例、資源），並用 **Progressive Disclosure（逐步載入）** 讓 Copilot 只在需要時把內容帶進上下文。

<br>

## 為什麼 Agent Skills 重要

我們可以把 Skills 想成「讓 Copilot 具備可被治理的能力」：

- **可重複**：同一套流程在不同對話、不同任務中被一致地使用。
- **可維護**：流程更新時只要改一個地方（Skill 目錄），不需要到處改 prompt。
- **可分享**：技能可以跟著 repo 走，也可以複製到其他 repo 或分享給團隊。
- **可組合**：多個技能可被同一個任務同時載入，形成更完整的工作流。

更重要的是：Skills 不是只教「要做什麼」，而是能把「怎麼做」與「需要的資源」放在一起（scripts、templates、examples），讓 Agent 不只會說，也更容易真的執行。

<br>

## Skills 的優勢：不只是方便，而是更可靠

Skills 有幾個具有價值的要點：

1. **降低人工成本**：不用每次都手動貼流程，模型也不需要每次重新學一遍。
2. **把流程從「口耳相傳」變成「可 review 的文件」**：Skills 是 repo 裡的檔案，可以送出 PR、也能被 code review。
3. **更貼近 Agent 的工作方式**：Agent 做事需要流程、需要資源、需要可執行的工具，而不是一段漂亮的提示詞。

<br>

## Skill 會比較節省 token 嗎？

如果我們原本會把大量規則長期塞在 instructions 或每次對話都貼一大段 SOP，那 Skills 通常會更省 token，原因在於 VS Code / Copilot 對 Skills 的載入採用三層式的逐步揭露：

- **Level 1：Skill discovery**：只讀每個 Skill 的 ==name== 與 ==description==（前置 YAML frontmatter）。
- **Level 2：Instructions loading**：只有在模型判斷「這個任務跟某個 Skill 的描述相符」時，才載入 **`SKILL.md`** 的內容。
- **Level 3：Resource access**：技能資料夾裡的 scripts / examples / docs 只有在真的需要時才讀取。

換句話說：我們可以安裝很多 Skills，但每次任務只會載入少數相關內容，但 Skills 也不是零成本：

- 一旦被載入後，**`SKILL.md`** 的內容同樣會佔 context。
- 如果規則本來就很短（例如 1~2 句 prompt 內容），拆成 Skill 不一定更划算。

<br>

## GitHub Copilot 的 Skills 核心概念

<div class="warning">
<font color="black">
把特定領域／流程變成「可重用、可按需載入」的能力模組，讓 GitHub Copilot 在需要時自動套用。
</font>
</div>

對使用者來說：

- 只要描述目標（例如：幫我找出為什麼 CI 失敗），GitHub Copilot 會自己決定要載入哪個 skill。
- 不需要每次都手動指定「用哪個 SOP」。

對團隊來說：

- 同一個 repo 裡，大家對「怎麼 debug build」「怎麼寫 release note」「怎麼跑測試」會更一致。

<br>

## GitHub Copilot 的 Agent Skills 定義與規範

Skills 的組成是：一個資料夾 + 一個 SKILL.md。

目錄位置：

- ==.github/skills/==（GitHub Copilot 的 shared location）
- ==.claude/skills/==（Legacy location）

**`SKILL.md`** 需要 YAML frontmatter，至少包含：

- **name**（必填）：小寫，如果有空白就要以 `-` 分隔（例如：pipeline-debug），通常也會跟資料夾名稱一致，==最多 64 個字元==。
- **description**（必填）：清楚描述「能做什麼」以及「什麼時候用」，這會直接影響模型是否會選中並載入，==最多 1024 個字元==。

Skill 內容建議包含：

- 這個 Skill 在幫我們完成什麼
- 什麼情境要用
- 分步驟流程（step-by-step）
- 範例輸入 / 輸出
- 如何使用技能資料夾內的 scripts / resources

==description 寫得越精準，LLM 在解析判斷才會越準；建議可在 description 裡直接放「Use this when...」之類的觸發語句，或明確表示在什麼情境就要使用目前這個 skill。==

<br>

## 在 VS Code 中如何使用 Agent Skills

目前 Agent Skills 在 VS Code 屬於 experimental，在 **VS Code Insiders** 才有提供，而且預設是 disabled 記得把設定打開：

{% asset_img settings-skills.jpg %}


基本使用流程：

1. 在 repo 建立 **.github/skills/**。
2. 每個 skill 建立一個子資料夾，例如：**.github/skills/pipeline-debug/**。
3. 在子資料夾放入 **`SKILL.md`**（包含 YAML frontmatter 與內容）。
4. 在 VS Code 啟用相關設定（上面截圖的兩個選項）。
5. 在 Copilot Chat / Agent Mode 提需求，Copilot 會自動判斷並載入相關 skills。

==實際使用我們不需要手動「選擇 skill」，只要目前 prompt 的需求跟 **description** 對得上，就會在背後注入技能內容。==

<br>

## Instructions 和 Skills 的差異：什麼時候適合用 Skills

這兩者都能「教 Copilot」，但設計目的不同：

- **Custom instructions**：偏向「全域規範」。例如：程式撰寫規範、命名規則、資料夾結構、commit 訊息格式。
- **Agent Skills**：偏向「任務導向流程」與「可重用的能力」。例如：Debug CI、執行測試矩陣、部署步驟、產生特定格式報告。

什麼時候用 Skills 比較適合：

- 發現某個流程一直被重複使用（或一直重複的貼某一段相同的 SOP）。
- 流程裡包含「可執行」的內容（scripts、commands、templates）。
- 希望能跨環境重複使用（VS Code、GitHub Copilot CLI、Coding Agent...等）。
- 希望它能被版本控管與 review（就像管理程式碼一樣）。

<br>

## MCP 和 Skills 的差異

以比較簡單的方式來區分：

- **Skills**：教 Agent「怎麼做」（流程、規範、範例、資源），並在需要時把這些指引載入上下文。
- **MCP**：讓 Agent「能做」更多事（透過標準化協定連外部工具/系統），把行動能力變成可呼叫的工具。

它們不是互斥，而是互補：

- 我們可以用 Skill 定義一個 SOP，例如：「如何 debug pipeline failure」。
- 處理流程裡可以指示 GitHub Copilot 用 MCP 工具去查 log、列出 workflow runs、抓取建置輸出。

也因此在企業流程常見的組合是：==Skills 提供流程與判斷準則；MCP 提供跨系統的可執行能力。==

<br>

## SDLC 場景：CI 失敗的 Debug Playbook

假設團隊常見痛點是：PR 數量一多的時候，CI 失敗的原因常常要人工查找 log、比對環境再回頭修改。

我們就可以建一個 Skill：**.github/skills/ci-failure-debugging/** 內容包含：

- 先分類失敗類型（例如：程式碼分析/StyleCop 或 Roslyn warnings、單元/整合測試（xUnit/NUnit）失敗、MSBuild/SDK 編譯錯誤或目標框架不符、NuGet 打包/相依性衝突、部署到 Azure App Service/AKS/IIS 等環境問題）。
- 針對不同類型指定查核順序
- 指定輸出格式（例如：「根因、證據、修復建議、是否可重現」）

Skill 流程可以如下列示意：

1. 先找出失敗的 job 與第一個 error
2. 如果是測試失敗：
 2.1. 找出最早的 failing test case
 2.2. 嘗試本機重跑（或用既有指令重跑）
3. 如果是 build 失敗：
 3.1. 先確認 SDK 版本是否一致
 3.2. 檢查最近的檔案變更
4. 產出一段固定格式的摘要（可貼到 PR comment）

如果目前環境有 MCP Server 可以使用（例如：GitHub MCP Server / Azure DevOps MCP Server），Skill 裡就能直接指定要呼叫哪些 tools、用什麼順序拿資料。這樣一來，GitHub Copilot 就不只是「猜原因」，而是「照著我們的 Playbook」把證據收集完整，並用一致格式輸出，大幅提升在 SDLC 裡的工作流程效率。

<br>

## 總結

Agent Skills 的價值不在於「又多了一個客製化機制」，而在於它把 AI 協作從「一次性的 prompt」推進到「可重用、可治理、可分享的能力模組」。

- 想要省 token：把冗長的 SOP 從常駐在 Instructions 移到依需求載入的 Skills。
- 想要更可靠：用 Skills 把流程固定下來，讓 GitHub Copilot 依 SOP 做事。
- 想要更能做事：用 MCP 擴充工具能力，再用 Skills 把工具串成工作流。

如果大家已經有一套在團隊內反覆被使用的 SOP（測試、除錯、部署、風險檢查），就可以這些開始著手第一個 Skill：小規模、可驗證，且能帶來立即收益的嘗試；如果還不知道怎麼開始起手式也沒關係，就到下方參考連結 Anthropics 的 Skills repo 直接找範例體會看看，相信一定可以很快上手的！

<div class="tips">
<font color="black">
官方文件：<br>
<a href="https://code.visualstudio.com/docs/copilot/customization/agent-skills" target="_blank">⁍ VS Code | Use Agent Skills in VS Code</a><br>
<a href="https://docs.github.com/en/copilot/concepts/agents/about-agent-skills" target="_blank">⁍ GitHub Docs | About Agent Skills</a><br>
<a href="https://github.com/anthropics/skills" target="_blank">⁍ GitHub | Public repository for Agent Skills</a>
</font>
</div>