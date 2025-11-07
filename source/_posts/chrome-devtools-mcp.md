---
title: Chrome DevTools MCP
date: 2025-10-09 16:13:48
tags: MCP
---

<img src="/post/2025/10/chrome-devtools-mcp/ChromeDevTools.jpg" align="left" style="margin-right: 10px; width: 125px; border: 1px solid gray;"/> 

無論是前端或後端開發工程師，相信大家都經常在瀏覽器中按下 **`F12`** 使用 Developer Tool。這個開發者工具不僅能檢視與調整頁面上的 HTML 元素，也能協助測試與偵錯前端 JavaScript。此外，還可以模擬各種裝置的瀏覽情境，或限制網路速度以模擬極端的使用狀況。這些功能都包含在 Chrome DevTools MCP 提供的工具清單中，本文將帶大家快速了解這些工具在實際開發場景中的應用方式。

<!-- more -->

<br>

## 主要特色
透過 MCP 提供的多元服務串接，我們可以以自然語言對話的方式，為 LLMs 帶來更多應用可能。目前 Chrome DevTools MCP 主要涵蓋三大面向：**自動化操作**、**效能洞察**，以及**瀏覽器操作與偵錯**。不論是頁面上的 HTML 元素、操作過程的截圖，或是網路請求的詳細資訊，都能作為上下文 (Context) 的來源，協助我們運用 LLMs 執行各種任務。

環境需求：
- Node.js v20.19 以上
- Chrome 瀏覽器
- npm

MCP 設定：
```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": ["-y", "chrome-devtools-mcp@latest"]
    }
  }
}
```

Chrome DevTools MCP 目前分為 6 個類別，提供如下列 26 個工具：

### Input automation

| 工具名稱 | 功能簡介 |
| :-- | :-- |
| click | 點擊指定元素 |
| drag | 拖曳元素到指定位置 |
| fill | 填入欄位或選取項目 |
| fill_form | 批量填寫多個表單元素 |
| handle_dialog | 處理彈出對話框 |
| hover | 滑鼠懸停於元素上 |
| upload_file | 於檔案欄位上傳檔案 |

使用 Input automation 可以讓我們操作鍵盤 (**`fill`** , **`fill_form`**)、滑鼠 (**`click`** , **`drag`** , **`hover`**) 在頁面上的各種行為，例如：
```
開啟 https://www.tenlong.com.tw/ 在 keyword 欄位輸入 "ai agent" 並送出 search 查詢動作。
```

{% asset_img example01.jpg %}

在上面截圖的執行過程可以看到 LLMs 理解我們所輸入的自然語言後，打開了 Chrome 瀏覽器自動使用了 **`fill`** 和 **`click`** 這兩個工作來達成任務，除此之外，因為 prompt 內容其實並沒有講得很清楚(沒有明確指定頁面元素ID)，所以它自己調用了 **`take_snapshot`** 截圖識別頁面內容來判斷下一步該如何進行，並回傳結果。

### Navigation automation

| 工具名稱 | 功能簡介 |
| :-- | :-- |
| close_page | 關閉分頁 |
| list_pages | 列出所有分頁 |
| navigate_page | 跳轉至指定網址 |
| navigate_page_history | 分頁歷史紀錄 |
| new_page | 開啟新分頁 |
| select_page | 設定操作目標分頁 |
| wait_for | 等待頁面出現指定文字 |

透過 Navigation automation 可以協助我們管理操作多個分頁、控制分頁歷程以及在適當時機等待頁面內容出現，常見應用包括自動化測試流程、抓取需要登入或多頁互動的資料，以及在多分頁工作流程中切換操作目標。此類工具可讓 LLMs 以自然語言指令完成跨分頁的步驟：開啟新分頁、在目標分頁中等待特定文字出現、切換回原分頁或關閉不再需要的分頁，確保後續動作的執行，例如：
```
1.開啟 https://www.books.com.tw 並在 id 為 "key" 的文字方塊中輸入 "ai agent"，按下 title 為 "搜尋" 的按鈕。
2.在新分頁開啟查詢列表第一筆的連結，回傳：書名、作者、出版日期、定價、優惠價。
```

{% asset_img example02-1.jpg %}
<br>
{% asset_img example02-2.jpg %}

### Emulation

| 工具名稱 | 功能簡介 |
| :-- | :-- |
| emulate_cpu | 模擬 CPU 效能/限速 |
| emulate_network | 模擬網路狀況 |
| resize_page | 調整頁面視窗尺寸 |

我們在前端開發時常會需要模擬不同裝置(解析度)來確認版型是否正確顯示，以往我們會需要手動切換，甚至建一個新的裝置設定來配合測試，現在就可以用自然語言表達，並讓 LLMs 來幫我們 snapshot 截圖。
```
使用 iPhone 13 Pro、iPad 6、FullHD 三種檢視方式打開 https://developer.chrome.com/blog/chrome-devtools-mcp?hl=zh-tw 
並截圖儲存在目前工作目錄中，檔名使用 yyyyMMdd-裝置名稱.jpg。
```

{% asset_img example03.jpg %}

### Performance

| 工具名稱 | 功能簡介 |
| :-- | :-- |
| performance_analyze_insight | 詳細分析效能洞見 |
| performance_start_trace | 開始頁面效能記錄 |
| performance_stop_trace | 停止頁面效能記錄 |

上面有一個模擬網路狀況 **`emulate_network`** 在實務上也蠻常使用的，可以模擬在網路傳輸速度不佳或是特定的網路傳輸情境，以確認我們的頁面呈現或是功能互動會不會因為傳輸過久而造成什麼問題，再搭配 **`performance_start_trace`** 和 **`performance_stop_trace`** 就可以很輕易的就達成測試並記錄數值的需求。
```
模擬以 "Fast 4G" 開啟 https://developer.chrome.com/blog/chrome-devtools-mcp?hl=zh-tw 
確認整頁載入需要花費多少時間，不需要設定 timeout 時間，直到整頁載入完成為止。
```

{% asset_img example04_05.jpg %}

### Network

| 工具名稱 | 功能簡介 |
| :-- | :-- |
| get_network_request | 取得指定的請求資訊 |
| list_network_requests | 列出當前頁面網路請求 |

程式開發人員工具中的 Network 可以看到頁面載入的所有資訊，在介面上也可以透過各種條件篩選或是排序來找到我們想要分析的目標，現在 Chrome DevTools MCP 也提供了對應的單筆、多筆查詢，應用上可以像是：
```
打開 https://www.momoshop.com.tw/ 分析頁面中哪一個項目的 response time 最久?
整個頁面載入使用多少傳輸量、多少時間?
```

{% asset_img example06.jpg %}

### Debugging

| 工具名稱 | 功能簡介 |
| :-- | :-- |
| evaluate_script | 執行 JavaScript 程式片段 |
| list_console_messages | 列出 console 訊息 |
| take_screenshot | 擷取頁面螢幕截圖 |
| take_snapshot | 取得當前頁面元素快照 |

前面幾張執行過程截圖當中都有穿插呼叫 **`evaluate_script`**，它主要是注入 JavaScript 程式片段執行並讀取回傳結果，內容當然可以自己撰寫提供給 LLMs 呼叫，但比較多數的情況可能會是 LLMs 理解完我們所提供的上下文(Context)後，為了達到最終任務目的自己加上的。這裡有兩個名稱很像的 tool：
- **`take_screenshot`**：就是把目前呈現的畫面內容截圖，可以儲存成 PNG、JPEG、WebP。
- **`take_snapshot`**：將目前畫面元素以記錄 uid 的方式將所有相關屬性記錄下來，這個適合用來作為上下文提供給 LLMs 使用，不適合人工閱讀。

{% asset_img example07.jpg %}


## 總結

Chrome DevTools MCP 讓自然語言操控瀏覽器自動化、效能分析與偵錯成為可能，將原本需要撰寫程式或熟悉工具操作的流程，轉化為直覺、可被人類語句驅動的工作流程。透過 Input / Navigation / Emulation / Performance / Network / Debugging 等工具組合，LLMs 能理解任務意圖、自動擷取頁面上下文並執行相應操作，極大地降低了自動化門檻。<br>
對一般使用者來說，要更容易達成自動化需求可以採取幾個實用做法：
- 以明確且分步的自然語言描述任務，將複雜流程拆成小步驟（例如：導航 → 填表 → 等待 → 截圖）。
- 善用快照與截圖作為上下文回饋，讓 LLM 有更精準的操作依據。
- 建立常用 prompt 與範本（templates），將成功流程模組化、重複使用。
- 先從小範圍任務切入（如：自動化登入或資料擷取），驗證後再串接更複雜的操作或流程。
- 為關鍵步驟加入驗證與錯誤處理（檢查元素存在、逾時重試），確保穩定性與安全性。

<br>結合自然語言與 Chrome DevTools MCP，可以讓更多人不論工程背景與否，更快速地做到瀏覽器自動化與偵錯，提升開發效率並把重複性工作交給工具處理，讓團隊專注在更具價值的設計與判斷。
<br>
<div class="tips">
<font color="black">
<a href="https://developer.chrome.com/blog/chrome-devtools-mcp?hl=zh-tw" target="_blank">⁍ AI 代理程式適用的 Chrome 開發人員工具 (MCP)</a><br>
<a href="https://github.com/ChromeDevTools/chrome-devtools-mcp" target="_blank">⁍ GitHub | ChromeDevTools MCP</a><br>
</font>
</div>
