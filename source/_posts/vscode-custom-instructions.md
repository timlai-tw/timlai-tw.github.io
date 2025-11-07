---
title: VS Code 的 Custom Instructions
date: 2025-11-01 11:11:24
tags: 
 - VSCode
 - GitHub Copilot
---

<img src="/post/2025/11/vscode-custom-instructions/vscode-github-copilot.jpg" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 

隨著 AI 輔助開發工具的普及，GitHub Copilot 已成為許多開發者日常工作中不可或缺（CP值最高）的助手。在預設情況下 GitHub Copilot 僅能提供通用的程式碼建議與回應。為了讓 AI 更貼近專案需求、遵循團隊的程式碼風格，以及提升開發效率，VS Code 提供了多種自訂機制，讓開發者能夠客製化 GitHub Copilot 的行為。本文將介紹 VS Code 中 GitHub Copilot 可以自訂的幾種方式：Custom Instructions、Prompt Files 以及 Chat Modes，協助大家打造更符合個人或團隊需求的 AI 開發環境。

<!-- more -->

<br>

GitHub Copilot 可以依據上下文提供程式碼建議，但每個專案或團隊都有其獨特的需求：不同的程式碼風格、特定的開發框架、安全性檢查標準，或是團隊約定的最佳實踐。透過自訂機制我們可以：

- **統一程式碼風格**：確保生成的程式碼符合專案規範。
- **提升工作效率**：減少重複性的提示輸入，讓 AI 自動遵循既定規則。
- **強化專案一致性**：讓所有團隊成員使用相同的 AI 輔助標準。
- **擴展 AI 能力**：透過工具整合與外部服務連接，讓 AI 執行更複雜的任務。

在 VS Code 中我們可以從最基本的設定逐步延伸到進階的工作流程，依需求打造專屬的 AI 開發環境。VS Code 的工作區設定通常儲存在專案根目錄下的 **`.vscode`** 資料夾；而 GitHub Copilot 的客製 instructions、prompt 與 chat modes 則常放在 **`.github`** 目錄中。順帶一提，VS Code 的使用者設定也可以放在系統的使用者設定（User Profile）路徑：
```text
VS Code
%APPDATA%\Code\User\settings.json
%APPDATA%\Code\User\prompts\*.md
--
VS Code Insider
%APPDATA%\Code - Insiders\User\settings.json
%APPDATA%\Code - Insiders\User\prompts\*.md
```

{% asset_img custom-instructions.jpg %}

## Custom Instructions

Custom Instructions 讓我們可以在 Markdown 檔案中定義通用規則，這些 instructions 會自動套用到 AI 產生程式碼的方式以及處理其他開發任務的行為。相較於在每次對話中手動重複提供相同的上下文，Custom Instructions 能確保 AI 回應始終符合我們程式碼實作與專案需求。需要注意的是 Custom Instructions ==**不會套用於編輯器中的程式碼自動完成功能（code completions），僅影響聊天互動的回應內容**==。

### Instructions 類型

VS Code 支援下列基於 Markdown 的 instructions 檔案，這些檔案可以同時存在，VS Code 會將它們合併並加入到聊天的上下文中：

| 檔案類型 | 說明 |
| :-- | :-- |
| .github/copilot-instructions.md | 單一檔案且檔名與路徑都要符合規定，會自動套用於工作區中的所有聊天請求。 |
| .instructions.md | 可依據實際需求建立多個檔案，針對特定任務或檔案使用，透過 **`applyTo`** 定義適用範圍。 |
| AGENTS.md （experimental）| 可以在工作區中定義多個 AI agent 的使用情境。 |

### 使用 copilot-instructions.md

適合定義全域的規範（例如：目前專案的 Coding Guidline）, 在工作區根目錄建立 **.github/copilot-instructions.md** 檔案後，VSCode 就會自動將其套用到所有聊天請求中。

**設定步驟：**

1. 啟用 VS Code 設定 **github.copilot.chat.codeGeneration.useInstructionFiles**（預設是已啟用)。
2. 在工作區根目錄建立 **.github/copilot-instructions.md** 檔案。
3. 使用 Markdown 格式以自然語言描述內容。

**以 C# Coding Guidline 為例：**

```markdown
# 專案程式碼規範（C# / .NET 8）

- 使用 C# 開發（目標平台：.NET 8），專案建議啟用 Nullable Reference Types（在 csproj 中設定 `<Nullable>enable</Nullable>`）
- 遵循 `.editorconfig` 與 Microsoft 建議（使用 `dotnet format`、Roslyn analyzers，如 `Microsoft.CodeAnalysis.NetAnalyzers`），並在 CI 中自動檢查格式與規則
- 命名規則：
  - 公開型別（類別、結構、介面）、方法、屬性、事件：PascalCase（介面以 `I` 前綴）
  - 私有欄位使用 `_camelCase`（底線 + camelCase）或 `camelCase`，局部變數使用 `camelCase`
  - 非同步方法以 `Async` 後綴（例如：`GetItemsAsync`）
- 所有公開 API 與重要方法應加上 XML 文件註解（`///`），說明用途、參數與回傳值，以利產生文件與使用者理解
- 優先使用 `async`/`await`，避免使用 `async void`（事件處理器除外）；所有可取消的非同步方法應接收 `CancellationToken`
- 使用依賴注入（DI）與介面抽象，避免全域靜態狀態；以相依反轉提高可測性
- 資源釋放使用 `using` / `await using` 或透過 DI 管理生命週期，避免手動忘記 Dispose
- 序列化建議使用 `System.Text.Json`（除非有特殊需求才考慮 Newtonsoft.Json），並注意用 JsonSerializerOptions 設定
- 例外處理：不要靜默吞錯；捕獲例外時要有明確策略（記錄、重試或以 Domain-specific 例外包裝再拋出）
- 日誌請使用結構化日誌（例如：Microsoft.Extensions.Logging），在日誌中記錄足夠的上下文，不要記錄敏感資訊
- 測試：為核心邏輯撰寫單元測試、為重要整合與端對端流程建立整合測試，CI 中執行 `dotnet test`
- 效能考量：避免不必要的複製與分配；在熱路徑考慮使用 `Span<T>/Memory<T>` 與 pooling 技術，但以可讀性為優先
- 安全性：不要在原始碼中放置密碼或金鑰；使用 Secret Manager、環境變數或外部秘密管理系統（如 Key Vault）
- 避免使用 `dynamic` 或 `unsafe`，除非有充分理由並在程式碼中加上註解與測試
```

### 使用 .instructions.md

當我們需要針對不同的程式語言、框架或任務建立專屬指示時，可以使用 **.instructions.md** 檔案。==透過 **applyTo** 屬性，可以指定套用的檔案或路徑範圍==。

**檔案結構：**

```markdown
---
description: "Python 專案程式碼規範"
applyTo: "**/*.py"
---

# Python 程式撰寫風格

- 遵循 PEP 8 風格指南
- 優先考慮可讀性與清晰度
- 為每個函式撰寫清晰簡潔的註解
- 確保函式名稱具有描述性並包含型別提示
- 保持適當的縮排（每層使用 1 個 Tab 間隔）
```

**建立方式：**

1. 在 Chat 中選擇 **Configure Chat > Instructions > New instruction file**
2. 選擇儲存位置（Workspace 或 User Profile）
3. 輸入檔案名稱並撰寫指示內容
4. 使用 applyTo 定義套用的範圍（例如：**\*.ts** , **\*.tsx**）

==**注意事項**==
過去我們習慣把各種自訂情境集中寫在 **.github/copilot-instructions.md**，但這樣容易造成不必要的 token 浪費或增加模型誤判（幻覺）的風險。現在可以透過建立多個獨立的 instruction 檔案來區分不同情境，並以 **applyTo** 指定要套用的檔案或路徑。不過在定義 custom instruction 時，要特別留意 **applyTo** 的範圍設定。例如下圖所示，本來只想針對特定檔案執行程式碼審查（review）以檢查是否符合 coding guideline，但若其他情境也設定了相同的 **applyTo**（例如都指定 *.cs），就可能出現規則衝突或語意矛盾，而這類上下文衝突往往是造成 AI 幻覺的主要原因。

{% asset_img review-coding-guideline.jpg %}


### 設定檔

對於 Custom Instructions 我們也可以透過 VS Code 的使用者設定或工作區設定來定義。在設定檔（VS Code 的 **settings.json**）中，既可以直接把內容輸入到 **`text`** 屬性，也可以用 **`file`** 屬性指定實體檔案路徑。此外，**Code generation** 與 **Test generation** 已明確建議改用 Instruction files，並標示為 deprecated（已棄用）。

| 使用情境 | 設定項目 |
| :-- | :-- |
| Code review | github.copilot.chat.reviewSelection.instructions |
| Commit message generation | github.copilot.chat.commitMessageGeneration.instructions |
| Pull Request title and description generation | github.copilot.chat.pullRequestDescriptionGeneration.instructions |
| Code generation **(deprecated)** | github.copilot.chat.codeGeneration.instructions |
| Test generation **(deprecated)** | github.copilot.chat.testGeneration.instructions |

**設定範例：**

```json
{
  "github.copilot.chat.pullRequestDescriptionGeneration.instructions": [
    { "text": "Always include a list of key changes." }
  ],
  "github.copilot.chat.reviewSelection.instructions": [
    { "file": "guidance/backend-review-guidelines.md" },
    { "file": "guidance/frontend-review-guidelines.md" }
  ]
}
```

{% asset_img custom-instructions-settings.jpg %}

### 撰寫技巧

- 保持指示簡短且獨立，每條指示應該是單一、簡單的陳述。
- 針對特定任務或程式語言，使用多個 **.instructions.md** 檔案，並透過 **`applyTo`** 屬性選擇性套用。
- 將專案特定的指示儲存在工作區中，以便與團隊成員共享並納入版本控制。
- 在 Prompt Files 和 Chat Modes 中參考 Instructions 檔案，保持內容簡潔並避免重複。

## Prompt Files

Prompt Files 是可重複使用的 Markdown 檔案，可以定義我們常用、可重複執行的任務。與 Custom Instructions 不同的是，Prompt Files 是獨立的提示，可以在聊天中直接執行。我們可以在其中包含任務特定的上下文與執行指引，並結合 Custom Instructions 確保複雜任務的一致執行，在 VS Code 的 GitHub Copilot 對話視窗中只要輸入 **`/`** 就可以選擇自訂的 Prompt Files。

### 檔案結構

Prompt Files 使用 ==**.prompt.md**== 副檔名，結構如下：

**YAML frontmatter**

```yaml
---
description: "建立 React 表單元件"
mode: "agent"
model: "Claude Sonnet 4"
tools: ["search", "fetch", "git"]
---
```

| 欄位 | 說明 |
| :-- | :-- |
| description | 提示的簡短描述。 |
| mode | 聊天模式：`ask`、`edit` 或 `agent`（預設）。 |
| model | 執行提示時使用的語言模型，若未指定則使用目前選擇的模型。 |
| tools | 可使用的工具或工具集名稱陣列。 |

**Markdown 格式的提示指示**

在主體中可以使用變數，例如：
- 工作區變數：`${workspaceFolder}`、`${workspaceFolderBasename}`
- 選取變數：`${selection}`、`${selectedText}`
- 檔案上下文變數：`${file}`、`${fileBasename}`、`${fileDirname}`
- 輸入變數：`${input:variableName}`、`${input:variableName:placeholder}`

### 建立 Prompt File

**步驟：**

1. 啟用設定 **chat.promptFiles** （預設已啟用，且路徑是 .github/prompts）。
2. 在 Chat 視窗中選擇 **Configure Chat > Prompt Files > New prompt file**。
3. 選擇儲存位置（Workspace 或 User Profile）。
4. 輸入檔案名稱並使用 Markdown 格式撰寫提示內容。

**範例：**

```markdown
---
description: "建立可重複使用的 React 表單元件"
mode: "agent"
tools: ["search", "edit"]
---

# 建立 React 表單元件

請建立一個可重複使用的 React 表單元件，具有以下特性：

1. 使用 TypeScript 並包含完整的型別定義
2. 支援表單驗證（使用 React Hook Form）
3. 包含錯誤訊息顯示
4. 具有可自訂的樣式屬性
5. 提供使用範例與說明文件

元件名稱：${input:componentName:FormComponent}
```

### 使用 Prompt File

有三種方式可以執行 Prompt File：

1. **在 Chat 視窗中輸入**：在聊天輸入欄位中輸入 `/` 後接 prompt file 名稱和參數，例如：
   ```
   /create-react-form: componentName=LoginForm
   ```
2. **使用命令面板**：執行 **Chat: Run Prompt** 命令並從列表中選擇 prompt file。
3. **直接執行**：在編輯器中開啟 prompt file，點擊編輯器標題列的播放按鈕。

## Chat Modes

Chat Modes 可以為特定角色或任務建立專屬的 AI 助理，在 Markdown 檔案中我們可以定義其範圍與能力、可存取的工具以及語言模型。不同的任務需要不同的能力，例如在規劃階段可能只需要唯讀工具進行研究與分析以防止意外的程式碼變更，而實作模式則需要完整的編輯能力。Chat Modes 讓我們能夠精確指定每個任務可用的工具，確保 AI 具備適合該工作的能力。

### Chat Mode 檔案結構

Chat Mode 檔案使用 ==**.chatmode.md**== 副檔名，結構如下：

**YAML frontmatter**

```yaml
---
description: "建立實作計畫"
tools: ["search", "fetch"]
model: "Claude Sonnet 4"
handoffs:
  - label: "開始實作"
    agent: "implementation"
    prompt: "請根據上述計畫開始實作"
    send: false
---
```

| 欄位 | 說明 |
| :-- | :-- |
| description | Chat mode 的簡短描述，顯示在聊天輸入欄位的文字。 |
| tools | 可用的工具或工具集名稱列表。 |
| model | 執行提示時使用的 AI 模型。 |
| ==handoffs== （experimental） | 可選的後續動作或提示列表，用於在 Chat Modes 之間轉換。 |

### Handoffs

Handoffs 是 VS Code Insiders 版本的實驗性功能，讓我們能夠建立引導式的順序工作流程，在不同 Chat Modes 之間轉換並提供建議的下一步操作。在聊天回應完成後，會出現交接按鈕，讓使用者可以帶著相關上下文提示移動到下一個模式。以軟體開發流程為例，我們可以設計如下三個 Chat mode：
- 規劃（planning）— 產出任務清單與接受準則。
- 實作（implementation）— 產生/修改程式碼、建立測試、建立 branch。
- 程式碼審查（review）— 讀取程式碼變更、產生 review 建議和 PR 描述。

透過 Handoffs 可以讓我們在「規劃」完成後，一鍵把上下文（任務清單、接受準則、檔案清單）傳到「實作」模式，或從「實作」傳到「審查」模式，讓下一個模式能立即取得足夠的上下文與行動指示（也可選擇自動執行或先由使用者確認）。

**planning.chatmode.md**
```yaml
---
description: "規劃模式 — 擬定需求與任務拆分"
tools: ["search"]
model: "default"
handoffs:
  - label: "開始實作"
    agent: "implementation"
    prompt: |
      我已完成規劃。請以下列任務清單開始實作：
      任務清單：
      {{tasks}}
      接受準則：
      {{acceptance}}
      請建立一個實作任務（branch + 主要檔案列表），並在實作模式中列出需要產生或修改的檔案與測試。
    send: false
---
```

**implementation.chatmode.md**
```yaml
---
description: "實作模式 — 產生程式碼、測試與 commit"
tools: ["edit", "git", "search"]
model: "default"
handoffs:
  - label: "送交審查"
    agent: "review"
    prompt: |
      已完成實作，請啟動審查流程：
      - 列出本次變更檔案
      - 產生 PR 標題與描述（包含變更重點、測試覆蓋與注意事項）
      - 若發現明顯問題請直接在 review 模式中提出建議
    send: false
---
```

**review.chatmode.md**
```yaml
---
description: "審查模式 — 生成審查建議與 PR 文案"
tools: ["search"]
model: "default"
handoffs: []
---
```

handoffs 屬性說明：
- label：在 Chat 介面會看到的按鈕名稱。
- agent：目標 Chat Mode 的識別名稱（通常與對應 .chatmode.md 的檔案名或 agent 名稱一致）。
- prompt：傳給目標模式的內容（建議使用簡短清楚的指示，也可用變數替換）。
- send：
  - true 表示使用者點按後立即將 prompt 送出並執行。
  - false 表示只切換到目標模式並把 prompt 放在輸入框，讓使用者檢視/修改後再發送。

### 建立 Chat Mode

1. 在 Chat 視窗中選擇 **Configure Chat > Modes > Create new custom chat mode file**。
2. 選擇儲存位置（Workspace 或 User Profile）。
3. 輸入 Chat Mode 的名稱（此名稱會顯示在 Chat 視窗的 Chat Mode 下拉選單中）。
4. 在新建立的 **.chatmode.md** 檔案中提供詳細資訊。


### 工具列表優先順序

當 chat mode 和 prompt file 都指定了 **tools** 欄位時，可用工具的優先順序如下：
1. Prompt file 中指定的工具（如果有）。
2. Prompt file 中參考的 Chat Mode 的工具（如果有）。
3. 所選 chat mode 的預設工具。

## 客製化的實際應用情境

| 使用情境 | 建議方法 |
| :-- | :-- |
| 專案級程式碼規範 | Custom Instructions |
| 程式語言或框架特定規則 | Custom Instructions（搭配 glob patterns） |
| 可重複使用的開發任務 | Prompt Files |
| 以規劃或研究模式使用聊天 | Custom Chat Modes |
| 定義專門的工作流程 | Custom Chat Modes |
| 整合外部服務 | MCP and Tools |

## 入門建議

### 1. 嘗試不同的語言模型

從實驗不同的語言模型開始，為不同類型的工作獲得更好的結果。使用聊天中的模型選擇器切換模型，簡單任務使用較快的模型，複雜推理使用更強大的模型，例如：一般工作任務可以使用 GPT-5 mini (0x)，實際要產生程式碼再選擇 Claude Sonnet 4.5 (1x)。

### 2. 設定基本指引

建立 Custom Instructions 可讓所有與 LLM 的聊天互動產生一致的結果。建議在工作區建立一個包含程式碼規範與偏好的 **.github/copilot-instructions.md**，以改善聊天回應；一般來說，整個專案應遵循的規範可在此定義。

### 3. 加入任務自動化

識別重複性任務後，為常見工作流程建立 Prompt Files。就像開發時會把常用的程式碼區塊抽成共用元件一樣，在 GitHub Copilot 的情境中，這類流程可儲存為獨立的 Prompt Files 以便重複使用。

### 4. 擴展能力

當需要連接外部服務或執行特定操作時，可以加入 MCP servers 和工具來擴展 LLM 的輔助能力。

### 5. 建立自己的工作流程

我們也可以透過建立自訂的 Chat Modes 並結合特定工具、指示與上下文，針對特定角色或專案階段使用，甚至也可以透過 handoffs 的方式來實作一個像是 Spec-kit 但卻又更符合自己需求的工作流程。

## 結語

透過 Custom Instructions、Prompt Files 和 Chat Modes 三種機制，VS Code 中的 GitHub Copilot 能夠從通用的 AI 助手轉變為貼近專案需求、遵循團隊規範的專屬開發夥伴。Custom Instructions 提供全域或特定範圍的程式碼規範，Prompt Files 讓重複性任務標準化與自動化，而 Chat Modes 則為不同角色與工作流程建立專門的 AI 環境。這些客製化功能不僅提升了開發效率，也確保了團隊協作的一致性。從最簡單的設定檔開始，逐步建立適合你的 AI 開發環境，讓 GitHub Copilot 成為真正理解你需求的智慧助手。

==下面參考連結有一個 Awesome Copilot (非常重要)，裡面有豐富的 Instructions / Prompts / Chat Modes 可以參考使用，大家記得一定要去挖寶!==

<div class="tips">
<font color="black">
<a href="https://code.visualstudio.com/docs/copilot/customization/overview" target="_blank">⁍ Customize chat to your workflow</a><br>
<a href="https://code.visualstudio.com/docs/copilot/customization/custom-instructions" target="_blank">⁍ Use custom instructions in VS Code</a><br>
<a href="https://code.visualstudio.com/docs/copilot/customization/prompt-files" target="_blank">⁍ Use prompt files in VS Code</a><br>
<a href="https://code.visualstudio.com/docs/copilot/customization/custom-chat-modes" target="_blank">⁍ Custom chat modes in VS Code</a><br>
<a href="https://github.com/github/awesome-copilot" target="_blank">⁍ GitHub | Awesome Copilot</a>
</font>
</div>
