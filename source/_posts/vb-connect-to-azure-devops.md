---
title: 使用 VB5/VB6 連線至 Azure DevOps Server 2022 (TFVC)
date: 2025-04-17 10:45:13
tags: 
 - VB5
 - VB6
 - TFS
 - Team Explorer
 - MSSCCI Provider
---

<img src="/post/2025/04/vb-connect-to-azure-devops/VS_TFS.png" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 

Visual Studio 從 97 發展到現在的 2022 號稱地表最強開發工具，能夠與 TFS 或 Git 版控連線自然不是問題，最近剛好遇到客戶還有許多 VB5/VB6 開發的 Winform 程式需要維護，而且版控伺服器也從 TFS 2013 升級至 Azure DevOps Server 2022，藉此機會向大家整理說明 Visual Studio 各版本與 TFS / Azure DevOps 連線的需求與設定。
<br>

<!-- more -->

<br>

## 前言
我們使用 Visual Studio 開發工具能夠與 TFS 連線是因為它的功能包含了 **`Team Explorer`**，明確來說是從 Visual Studio 2005 首次引入 TFS (Team Foundation Server) 起才開始包含此功能，在這個版本之前則需要額外安裝 **Team Explorer** 和 **MSSCCI Provider** (Microsoft Source Code Control Integration) 才能夠在開發工具中與 TFS 進行整合。

## 版本相依性
作業系統、開發工具與版控伺服器彼此間有一定的版本相依性，Windows XP 無法安裝 Team Explorer 2013，只能安裝 Team Explorer 2010，搭配 MSSCCI Provider 2010 可以連線的版本有：TFS 2005 ~ 2018 和 Azure DevOps Server 2019/2020 ==(不包含2022)==，若舊開發環境 (Visual Studio 2013 以前) 想要與 Azure DevOps Server 2022 連線，必要最低門檻可以參考下列配置：
- **Windows 7 以上**
- **Team Explorer 2013**
- **MSSCCI Provider 2013**

Visual Studio 97/6/2003~2008 搭配 Team Explorer 2010 + MSSCCI Provider 2010 可以從開發工具連線至 TFS 2010~2017 和 Azure DevOps Server 2019/2020，若要連線至 Azure DevOps Server 2022 則需要搭配 Team Explorer 2013 + MSSCCI Provider 2013 才行，各版本 MSSCCI Provider 可以自 Visual Studio | Marketplace 取得：
<div class="tips">
<font color="black">
<a href="https://marketplace.visualstudio.com/items?itemName=TFSPowerToolsTeam.MicrosoftVisualStudioTeamFoundationServer2010MSSCC" target="_blank">⁍ Microsoft Visual Studio Team Foundation Server 2010 MSSCCI Provider 32-bit</a><br>
<a href="https://marketplace.visualstudio.com/items?itemName=TFSPowerToolsTeam.MicrosoftVisualStudioTeamFoundationServer2012MSSCC" target="_blank">⁍ Microsoft Visual Studio Team Foundation Server 2012 MSSCCI Provider 32-bit</a><br>
<a href="https://marketplace.visualstudio.com/items?itemName=TFSPowerToolsTeam.MicrosoftVisualStudioTeamFoundationServer20132015M" target="_blank">⁍ Microsoft Visual Studio Team Foundation Server 2013&2015 MSSCCI Provider 32-bit</a><br>
<a href="https://marketplace.visualstudio.com/items?itemName=TFSPowerToolsTeam.TeamFoundationServerMSSCCIProvider201064-bit" target="_blank">⁍ Team Foundation Server MSSCCI Provider 2010 64-bit</a><br>
<a href="https://marketplace.visualstudio.com/items?itemName=TFSPowerToolsTeam.MicrosoftVisualStudioTeamFoundationServer2012MSSCC-9956" target="_blank">⁍ Microsoft Visual Studio Team Foundation Server 2012 MSSCCI Provider 64-bit</a><br>
<a href="https://marketplace.visualstudio.com/items?itemName=TFSPowerToolsTeam.MicrosoftVisualStudioTeamFoundationServer20132015M-11387" target="_blank">⁍ Microsoft Visual Studio Team Foundation Server 2013&2015 MSSCCI Provider 64-bit</a><br>
</font>
</div>

## VB5/VB6

在 VB5/VB6 的工具列需要額外安裝上述提到的 MSSCCI Provider 2010/2013 才會出現 **Team Foundation** 選項，讓我們在開發工具中將程式碼與版控伺服器整合。
{% asset_img create-project-from-tfs.png %}

若出現下列錯誤訊息，就是 MSSCCI Provider 版本太舊了，無法連線到新的版控伺服器，例如：安裝 MSSCCI Provider 2010 但想要與 Azure DevOps Server 2022 連線，唯一解法就是安裝 Team Explorer 2013 + MSSCCI Provider 2013。

> TF14045: The identity 主機名稱\2ffd15ae-91e4-4df8-a346-e5be57e1340e is not a recognized identity.

{% asset_img not-a-recognized-identity.png %}

在實務情境可能還會遇到其他各種版本組合，經此次嘗試後，確認 VB5/VB6 都還可以與 Azure DevOps Server 2022 的 TFVC 版控進行整合，這邊有篇文章將各種組合條列得很詳盡，也提供大家參考。
<div class="tips">
<font color="black">
<a href="https://jessehouwing.net/azure-devops-connect-any-visual-studio-version/" target="_blank">⁍ Connect any version of Visual Studio to Azure DevOps or Azure DevOps Server</a><br>
</font>
</div>
