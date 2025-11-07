---
title: 使用 Playwright 測試框架 - 執行階段 (.NET)
date: 2025-05-27 19:20:14
tags:
 - Playwright
---

<img src="/post/2025/05/how-to-use-playwright-1/playwright.png" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 

[上一篇文章](/post/2024/11/how-to-use-playwright-1)提到了如何使用 Playwright 的測試產生器(codegen) 錄製腳本，除了可以運用在自動化測試之外，舉凡想要透過程式來自動執行的一連串行為，都可以透過 Playwright 來完成，本篇文章使用 Windows Sandbox 全新環境來執行由 .NET 8 撰寫的 Console 程式，讓大家知道建置產生執行檔後，還需要哪些必要的環境設定才可以正常讓程式運作。
<br>

<!-- more -->

<br>

## 安裝 .NET Runtime
不論是以前的 .NET Framework 或是現在的 .NET Core，在執行時都需要安裝 .NET Runtime 才能順利運行開發好的程式，以下圖 Console 專案建置後的執行檔為例，畫面還會很貼心的指引我們目前的需求版本和下載路徑，完成安裝後便可順利執行 Console 程式。
{% asset_img dotnet8.png %}
<br>

<div class="tips">
<font color="black">
<a href="https://dotnet.microsoft.com/download" target="_blank">⁍ 下載 .NET</a>
</font>
</div>

## 安裝 PowerShell
如果是全新的環境，安裝完 .NET Runtime 後應該還會出現如下圖的錯誤，主要原因是：
- pwsh 找不到路徑 (或是沒有安裝 PowerShell)
- 缺少 WebDriver (Edge/Chrome/Firefox) 
{% asset_img pwsh.png %}
<br>
若要安裝 PowerShell 可以依據自己的作業系統參考下列連結：
<div class="tips">
<font color="black">
<a href="https://learn.microsoft.com/zh-tw/powershell/scripting/install/installing-powershell-on-windows?view=powershell-7.5" target="_blank">⁍ 在 Windows 上安裝 PowerShell</a><br>
<a href="https://learn.microsoft.com/zh-tw/powershell/scripting/install/installing-powershell-on-linux?view=powershell-7.5" target="_blank">⁍ 在 Linux 上安裝 PowerShell</a><br>
<a href="https://learn.microsoft.com/zh-tw/powershell/scripting/install/installing-powershell-on-macos?view=powershell-7.5" target="_blank">⁍ 在 macOS 上安裝 PowerShell</a>
</font>
</div>

## 安裝 WebDriver
在上圖的錯誤訊息中有提到如下指令，路徑中的 **==netX==** 要替換為自己使用的版本路徑，而其中的 playwright.ps1 會存在於 .NET 程式建置後的 bin 目錄中，這個 .ps1 的主要用途是在 Windows 環境下執行 Playwright 測試，讓它可以正確找到 Playwright 主程式和相關驅動程式，確認 pwsh 指令可以運作後，就可以接著安裝 WebDriver 了。
``` 
 pwsh bin/Debug/netX/playwright.ps1 install
```
{% asset_img playwright.png %}
<br>
若在 install 之後沒有加上任何參數，則會安裝下列預設項目：

- Chromium
- Chromium Headless Shell
- Firefox
- Webkit
- FFMPEG
{% asset_img install.png %}
<br>
也可以依自己的需求安裝指定項目，可使用 install -h 查看相關資訊：
{% asset_img custom.png %}

## 總結
本文介紹了在全新環境下，如何讓 .NET 8 撰寫的 Playwright 測試程式順利執行，包含安裝 .NET Runtime、PowerShell 及 WebDriver 等必要步驟。只要依照上述流程完成環境設定，就能避免常見的錯誤訊息，順利執行自動化測試。希望這些說明能幫助你在不同平台上快速部署與運行 Playwright 測試專案。
