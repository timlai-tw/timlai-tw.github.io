---
title: Azure DevOps 的 Wiki 應用
date: 2025-10-30 15:28:39
tags:
 - Azure DevOps
 - Wiki
---

<img src="/post/2025/10/azure-devops-wiki/wiki.jpg" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 

在軟體開發專案中，文件管理與知識分享一直是團隊協作的重要環節。Azure DevOps 提供了強大的 Wiki 功能，讓團隊能夠在專案中建立、編輯和組織文件。無論是產品規格、技術文件、FAQ 還是操作手冊，Wiki 都能協助團隊將知識系統化地保存下來。更重要的是，Azure DevOps 的 Wiki 是建立在 Git 儲存庫之上，這代表我們也可以用管理程式碼的方式來管理文件，可以進行版本控制、分支管理等優勢。本文將介紹 Azure DevOps Wiki 的核心概念，包括 Wiki 在儲存庫中的連線位置、.order 檔案的用途，以及如何將資料夾升階為 Wiki 頁面。

<br>

<!-- more -->

<br>

## Wiki 的兩種類型

Azure DevOps 提供兩種類型的 Wiki：

### 1. 佈建的 Wiki (Provisioned Wiki)
當我們為專案建立 Wiki 時，Azure DevOps 會自動建立一個獨立的 Git 儲存庫來存放 Wiki 內容。這個儲存庫的命名慣例是 **==專案名稱.wiki==**，例如：專案名稱是 **`Contoso`**，則 Wiki 儲存庫名稱就是 **`Contoso.wiki`**。**==這個 Wiki 儲存庫不會出現在專案的 Repos 或 Code 區段的儲存庫下拉選單中，也不會列在專案設定的儲存庫頁面中==**。

### 2. 發佈的 Wiki (Published Wiki)
我們也可以將現有 Git 儲存庫中的 Markdown 檔案發佈為 Wiki。這種方式適合已經在儲存庫中維護技術文件或產品說明的團隊。透過發佈程式碼為 Wiki 可以：
- 將內容組織成受控的頁面順序結構。
- 瀏覽和篩選目錄。
- 發佈新版本的內容。
- 以管理程式碼的方式管理內容。
- 使用 Wiki 搜尋功能輕鬆地搜尋 Wiki。

每個專案可以發佈多個 Wiki，每個發佈的 Wiki 最多可以發佈 **==10 個分支==**。

## Wiki 在 Repo 的連線位置

### 佈建的 Wiki 儲存庫結構
Wiki 的 Git 儲存庫有以下特性：
- 儲存庫名稱：`專案名稱.wiki`
- 主分支名稱：`wikiMain`

我們可以透過在 wikiMain 分支上定義分支原則來管理 Wiki 儲存庫，也可以直接修改本機 wikiMain 分支內容，將它們直接推送至遠端分支。

### 存取 Wiki 儲存庫的方法
由於 Wiki 儲存庫不會顯示在一般的儲存庫清單中，但我們可以透過特定方式來存取：

1. 建立存取 URL：將以下 URL 中的 \<Organization\>、\<ProjectName\> 和 \<WikiName\> 替換為實際值：
   ```
   https://dev.azure.com/<Organization>/<ProjectName>/_git/<WikiName>
   ```

2. 從 Wiki 頁面存取：在 Wiki 頁面的「更多選項」(...)，選取「複製」(Clone wiki) 選項，即可取得 Wiki 儲存庫的 URL。

{% asset_img clone_wiki.jpg %}

## .order 檔案的用途

在 Wiki 中的 .order 檔案扮演著關鍵角色，它定義了 Wiki 頁面在目錄中的顯示順序，當資料夾沒有 .order 檔案時，Git 會使用預設的字母順序（A 到 Z）來排列頁面。

## .order 檔案的結構

.order 檔案是一個純文字檔案，每一行對應一個 Markdown 檔案的名稱（不含 .md 副檔名），例如：

```
Wiki-Welcome
Project-Overview
Get-Started
Submit-a-pull-request
team-contacts
readme-version-9
```

### 重要注意事項
1. **大小寫敏感**：頁面標題區有分大小寫，每行所條列的項目應與頁面標題和檔名中使用的大小寫一致。
2. **階層結構**：Wiki 根目錄的 .order 檔案指定根層級頁面的顯示順序，每個子資料夾也可以有自己的 .order 檔案來定義該資料夾的頁面順序。
3. **未列出的檔案**：.order 檔案中未列出的檔案會呈現在頁面順序中的最後。
4. **設定首頁**：**==將首頁名稱放在根目錄 .order 檔案的第一行，即可指定 Wiki 的首頁。==**

### 還原為字母順序
如果想要將自訂順序還原為字母順序，只需刪除 .order 檔案即可，刪除後 Git 會自動建立新的 .order 檔案，使用預設的字母順序。

### 資料夾注意事項
當我們透過 Git 將資料夾 push 到 Wiki 時，若資料夾內完全沒有任何檔案但是有其他資料夾，整個資料夾的顯示會是空白的。如下圖，在 Wiki 介面中只會看到 folder1 空目錄，並無法正確呈現 subfolder1 及其內容 file1.md 和 file2.md。

```text
|- folder1
|-- subfolder1
|   |-- file1.md
|   |-- file2.md
```
Wiki 呈現的結構
{% asset_img folder1-nofile.jpg %}

若要讓資料夾結構正常顯示，記得在每個資料夾內至少保持任一個有內容的檔案，如下圖在 folder1 目錄中新增 123.md。

```text
|- folder1
|- 123.md
|-- subfolder1
|   |-- file1.md
|   |-- file2.md
```
Wiki 呈現的結構
{% asset_img folder1-hasfile.jpg %}

## 如何將目錄升階至頁面

在 Azure DevOps Wiki 中，我們可以將儲存庫中的資料夾顯示為 Wiki 中的頁面，這個過程稱為「將資料夾升階至頁面」(Promote folder to page)，要將資料夾升階為頁面，需要滿足以下條件：

1. 同名的 Markdown 檔案：Wiki 中必須有一個 Markdown (.md) 檔案，其名稱與資料夾相同。
2. 位於相同位置：Markdown 檔案和資料夾必須是 Wiki 檔案清單中相同位置的對等項目（peers）。

<br>

延續上面提到的目錄結構，在下圖這樣的結構中，點選 folder1 並不會顯示任何內容，也沒有選項可以進行編輯。

```text
|- folder1
|- 123.md
|-- subfolder1
|   |-- file1.md
|   |-- file2.md
```
{% asset_img folder1-noContent.jpg %}

**==若想要編輯 folder1 資料夾的頁面，必需在同一層路徑中新增一個和目錄名稱相同的 .md (folder1.md)，這個檔案並不會顯示在 Wiki 列表中，而是會直接呈現在 folder1 資料夾的頁面。==**

```text
|- folder1
|- 123.md
|-- subfolder1
|   |-- file1.md
|   |-- file2.md
|- folder1.md
```
{% asset_img folder1-hasContent.jpg %}

## Wiki 版本管理

發佈的 Wiki 可以有多個版本，預設會顯示最新版本，我們可以從分支下拉式選單中選取要顯示的版本，由於 Wiki 是儲存在 Git 儲存庫，所有的變更都需要透過 commit 來完成，我們可以：
- 直接在 Azure DevOps Web 介面編輯 Wiki 頁面內容。
- 使用 Git 在本機編輯後 commit & push。
- 透過 Pull Request 的方式進行審核後再合併變更。

<br>

如果希望對 Wiki 進行更嚴格的控制，也可以在 Wiki 分支上定義分支原則，例如：
- 要求 Pull Request 審核。
- 要求建置驗證（Build Validation）。
- 自動連結工作項目。

## 結語

Azure DevOps 的 Wiki 功能為團隊提供了一個強大且靈活的知識管理平台。透過 Git 儲存庫的支援，Wiki 不僅能夠進行版本控制，還能整合到現有的開發工作流程中。.order 檔案讓我們可以自由地組織頁面結構，而資料夾升階功能則讓文件的層級關係更加清晰。無論選擇建立佈建的 Wiki 還是將現有的 Markdown 檔案發佈為 Wiki，Azure DevOps 都能滿足團隊在不同情境下的需求。善用這些功能，可以大幅提升團隊的知識分享效率和文件品質。

<div class="tips">
<font color="black">
<a href="https://learn.microsoft.com/zh-tw/azure/devops/project/wiki/wiki-create-repo?view=azure-devops&tabs=browser" target="_blank">⁍ 為您的專案建立 Wiki</a><br>
<a href="https://learn.microsoft.com/zh-tw/azure/devops/project/wiki/publish-repo-to-wiki?view=azure-devops&tabs=browser" target="_blank">⁍ 將 Git 存放庫發佈至 Wiki</a><br>
<a href="https://learn.microsoft.com/zh-tw/azure/devops/project/wiki/wiki-file-structure?view=azure-devops" target="_blank">⁍ Git 中的 Wiki 檔案和資料夾結構</a>
</font>
</div>
