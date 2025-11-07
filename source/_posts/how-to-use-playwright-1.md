---
title: 使用 Playwright 測試框架 - 環境準備 (.NET)
date: 2025-05-19 17:40:54
tags:
 - Unit Test
 - Playwright
---

<img src="/post/2025/05/how-to-use-playwright-1/playwright.png" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 


在現代軟體開發中，自動化測試已成為確保程式碼品質的重要一環，Playwright 是一個強大的自動化測試框架，支援 Chromium / Firefox / WebKit 瀏覽器，搭配測試產生器(codegen)讓開發者能夠輕鬆撰寫可靠且高效的測試腳本。本篇文章將介紹如何在 .NET 開發環境中設置和使用 Playwright，從安裝必要的套件到撰寫基本的測試案例，幫助大家快速上手這個工具。
<br>

<!-- more -->

<br>

## 使用 Visual Studio

如果是慣用地表最強開發工具，打開 Visual Studio 建立一個新的 **`MSTest 測試專案`**，讓我們有一個基本測試環境可以開始撰寫 Playwright 測試。
{% asset_img vs-01.png %}

<br>

要在 MSTest 使用 Playwright 必需要安裝 **`Microsoft.Playwright.MSTest`** 套件，這個套件會將 Playwright 與 MSTest 整合，使我們能夠在 MSTest 測試過程中使用 Playwright 功能。
{% asset_img vs-02.png %}

## 使用 .NET CLI

若是使用 VSCode 開發工具，我們可以用 **dotnet new** 指令新增一個 PlaywrightTests 專案，預設內容會包含一個 UnitTest1.cs 檔案。

```
dotnet new mstest -n PlaywrightTests
cd PlaywrightTests
```

安裝必要的 Playwright 相依套件：

```
dotnet add package Microsoft.Playwright.MSTest
```

{% asset_img cli-01.png %}

<br>

建置程式碼，確認環境準備是否有異常。
```
dotnet build
```
{% asset_img cli-02.png %}

<br>

## 安裝 Playwright CLI

在 .NET 開發環境中，雖然 **playwright.exe** 指令沒有像 **npx playwright** 那麼多參數與功能，但我們在錄製網頁操作過程來產生測試程式碼 (codegen)，或是開啟追蹤檔 (show-trace) 時仍會使用到，可以透過下列指令來安裝 **`Microsoft.Playwright.CLI`**：
```
dotnet tool install --global Microsoft.Playwright.CLI
```

上述套件預設會被安裝到目前的使用者範圍內的 .NET 全局工具路徑中，具體安裝位置會因作業系統而異：
```
Windows
C:\Users\<你的用戶名>\.dotnet\tools\

Linux / macOS
/home/<你的用戶名>/.dotnet/tools/
```
{% asset_img cli-03.png %}


我們可以直接開啟目錄確認工具是否安裝成功，或是使用以下指令來列出所有安裝的 .NET 工具：
```
dotnet tool list --global
```

例：
```
Package Id                    Version      Commands
-----------------------------------------------------
dotnet-ef                     8.0.8        dotnet-ef
microsoft.playwright.cli      1.2.3        playwright
```

## 移除 Playwright CLI

若需要移除已安裝的套件，可以使用下列指令：
```
dotnet tool uninstall microsoft.playwright.cli
```

## 安裝 Playwright 支援的瀏覽器

Playwright 支援 Chromium、WebKit 和 Firefox 瀏覽器，我們可以使用 **playwright install** 指令來安裝預設瀏覽器，這個動作會將瀏覽器安裝到 **==%LOCALAPPDATA%\ms-playwright==** 目錄下，若想要自訂安裝的項目，可以透過 **playwright help install** 來瞭解所有的選項。

```
playwright install
```
{% asset_img cli-04.png %}

<br>

```
playwright help install
```
{% asset_img cli-05.png %}

## 小試身手

以上 Playwright 環境設定都完成後，我們就可以直接用官網的第一個範例來小試身手：

- `HasTitle()` 
打開 Playwright 官方網站，檢查網站的標題是否包含特定的字串，確認網頁標題中是否包含 "Playwright" 這個字。

- `GetStartedLink()` 
一樣是打開 Playwright 官方網站，點擊一個名為 "Get started" 的連結，然後檢查點擊後的頁面是否有一個名為 "Installation" 的標題，以確保連結正確導向到預期的頁面。

大家應該不難發現 Playwright 的程式碼很直覺易讀，通常也不太需要夾雜 CSS class 或 style 來尋找定位網頁上的元素，除此之外還提供了豐富的 API 來進行各種操作，如：點擊、輸入文字、截圖等，之後再陸續向大家介紹各項重點特色，希望大家也都能快速上手 Playwright，並在日常開發中提升測試效率與程式碼品質。

```csharp
using System.Text.RegularExpressions;
using System.Threading.Tasks;
using Microsoft.Playwright;
using Microsoft.Playwright.MSTest;
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace PlaywrightTests;

[TestClass]
public class ExampleTest : PageTest
{
    [TestMethod]
    public async Task HasTitle()
    {
        await Page.GotoAsync("https://playwright.dev");

        // Expect a title "to contain" a substring.
        await Expect(Page).ToHaveTitleAsync(new Regex("Playwright"));
    }

    [TestMethod]
    public async Task GetStartedLink()
    {
        await Page.GotoAsync("https://playwright.dev");

        // Click the get started link.
        await Page.GetByRole(AriaRole.Link, new() { Name = "Get started" }).ClickAsync();

        // Expects page to have a heading with the name of Installation.
        await Expect(Page.GetByRole(AriaRole.Heading, new() { Name = "Installation" })).ToBeVisibleAsync();
    }
}
```
