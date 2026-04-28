# FB Page 發布流程（Chrome MCP 自動化）

> 透過 [Claude in Chrome MCP](https://docs.claude.com/en/docs/claude-code/mcp) 操作瀏覽器發 FB Page 貼文。
> 注意：FB DOM 變動頻繁，本文件描述的流程在 2026-04 驗證有效，未來可能需要更新。

---

## ⚠️ 「在想些什麼？」vs 便利貼（最重要的雷）

FB Page 上有**兩個看似相同但功能不同**的撰寫入口：

### ❌ 不要點：頂部「分享想法⋯⋯」氣泡

位於 Page cover 下方，你的個人頭像旁邊的小氣泡按鈕。
**這是 FB 新便利貼**（Sticky Note）功能，**限 60 字**，**不是貼文**！

點進去會看到：
- 標題寫「**新便利貼**」
- 字數限制顯示「**0/60**」
- 沒有 Reel / 直播 / 排程選項

### ✅ 正確點：捲下去後的「在想些什麼？」

捲到 Page 內容區（在 cover + 個人資料下方），會看到：
```
[頭像] 在想些什麼？
[相片／影片] [Reel] [直播視訊]
```

點「在想些什麼？」會開啟「**建立貼文**」modal，這才是真正的貼文撰寫。

---

## 完整發布流程（步驟）

### Step 1：導航 + 截圖確認

```
navigate(url=<YOUR_FB_PAGE_URL>, tabId=<TAB>)
wait 4 秒
screenshot
```

確認 Page 已載入（看到 Page 名稱 + cover）。

### Step 2：捲到「在想些什麼？」

cover 下方需要捲動才看得到撰寫框。通常 scroll down 約 3-4 ticks。

```
scroll(direction=down, amount=3)
wait 1 秒
screenshot
```

### Step 3：點「在想些什麼？」

點擊文字 placeholder「在想些什麼？」（不是相片/Reel/直播按鈕）。

通常座標位於頁面中央偏上（依視窗大小而定，約 x=1023, y=178）。

```
left_click(coordinate=[<x>, <y>])
wait 2 秒
screenshot
```

確認**「建立貼文」modal** 打開。標題顯示「建立貼文」+ 你 Page 名稱 + 「所有人」可見性。

### Step 4：點 textarea 聚焦

點 modal 中央偏上的「在想些什麼？」文字區域（不是 placeholder 字，是 textarea 區域）。

```
left_click(coordinate=[<x_of_textarea>, <y_slightly_below_placeholder>])
```

例：`left_click(coordinate=[775, 280])`

### Step 5：輸入草稿

```
type(text="<完整草稿內容含換行>")
```

注意：
- 換行符 `\n` 會被當段落分隔（不會送出）
- URL 會被 FB 自動轉成可點擊超連結
- 中文 / 英文混排沒問題

### Step 6：點「繼續」

modal 底部會有「繼續」按鈕（變藍 = 啟用）。點下去進入「貼文設定」頁。

```
left_click(coordinate=[<x>, <y_of_繼續>])
wait 2-3 秒
```

### Step 7：確認設定 + 點「發佈」

「貼文設定」頁顯示：
- 貼文預覽（你的草稿）
- Post audience：所有人
- 排程選項：立即發佈
- 加強推廣貼文：toggle off（不要打開）

捲到底部找「**發佈**」按鈕（藍色 full-width）。

```
scroll(direction=down, amount=5)
left_click(coordinate=[<x>, <y_of_發佈>])
wait 3 秒
```

### Step 8：驗證發布成功

reload Page 或 navigate 回去，用 JS 抓 `[data-ad-preview="message"]` 看第一篇是不是你剛發的。

```javascript
(() => {
  const allMsg = document.querySelectorAll('[data-ad-preview="message"], [data-ad-comet-preview="message"]');
  return JSON.stringify({
    count: allMsg.length,
    first_post: allMsg.length > 0 ? allMsg[0].innerText.trim().slice(0, 200) : null
  });
})()
```

如果 `first_post` 是你剛發的草稿開頭 → ✅ 發布成功。

---

## 戰績回查（2h / 24h / 48h）

發完 2h / 24h / 48h 後自動回 Page 查看：

1. 進 Page
2. 找剛發的那篇貼文
3. 抓觸及人數 / 讚 / 留言 / 分享數據
4. 寫回 `content_plan.md` 戰績欄位

公開觸及只能在「洞察報告」（Page Insights）看到。需要 Page 管理員權限。

```
navigate(url="<YOUR_FB_PAGE_URL>/insights")
```

---

## 常見錯誤

### Tab 凍結

連續 scroll + JS 太快會讓 FB DOM 凍結 → CDP timeout。

**處理方式**：
- 等 5 秒重試
- 重新 navigate 該 tab
- 最壞 close tab + open new tab

### 撰寫框位置變動

FB 偶爾改版會調整「在想些什麼？」位置。

**處理方式**：
- 用 `read_page(filter="interactive")` 找含「在想些什麼」字樣的 button
- 用 `find` 工具搜尋「在想些什麼」文字 ref
- fallback 到視覺座標（screenshot + 估）

### 發布失敗（卡在「貼文設定」）

可能原因：
- 內容含被檢測為違規的字（例：含某些政治詞 / 賭博詞 / 醫療誇大）
- 連續發太多同主題（FB 反 spam）
- Page 被限流

**處理方式**：
- 換內容重發
- 隔 1 小時再試
- 若多次失敗，手動發以排除自動化問題

### 「Enter = 送出」陷阱

FB / Threads **留言框**（不是 compose modal）的 Enter = 送出留言。

**留言永遠壓成單行**：
- ✅「重點 → 結論 → 細節」
- ✅「A，B，C」
- ❌ 換行的留言會變成多則留言

撰寫框 modal 的 Enter = 換行（沒問題）。

---

## DOM 選擇器參考（2026-04 有效）

| 元素 | 選擇器 |
|---|---|
| 貼文容器 | `[data-ad-preview="message"]` 或 `[data-ad-comet-preview="message"]` |
| 留言容器 | 沒有穩定 selector，需用 ARIA role |
| 撰寫 modal | `[role="dialog"]`（含「建立貼文」標題）|

⚠️ FB class names 已 obfuscated 不可用。盡量用 data-* attributes / ARIA role / 文字內容作為 selector。

---

## 開發紀錄

- v0.1（2026-04）— 「在想些什麼？」vs 便利貼陷阱記錄 + Chrome MCP 完整步驟
