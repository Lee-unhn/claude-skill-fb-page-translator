# Phase 1：學風格（FB Page 爬取 + 翻譯員轉換）

> 第一次使用 skill 時跑此流程：爬你 FB Page 既有貼文 → 萃取你的 voice → **轉成翻譯員版本** 寫入 `style_profile.md`。

---

## 1. 問網址

問使用者 FB Page 網址（`facebook.com/<username>` 或 `facebook.com/profile.php?id=<id>`）。

**不要猜、不要搜尋**。拿到後確認是 `facebook.com` 開頭。

---

## 2. 導航 + 捲動（重要：FB Page 虛擬化）

`navigate` 到使用者網址，等 4 秒。

**FB Page 會虛擬化捲過的貼文（從 DOM 卸載），要邊捲邊抓，不能先滾完再抓。**

捲動時滑鼠 hover 在右欄貼文區（座標約 `x=985, y=500`），不是左欄。

```
computer(action="scroll", coordinate=[985, 500], scroll_direction="down", scroll_amount=10)
wait 2 秒
```

---

## 3. 抓貼文

### 可用 selector（2026-04 有效）

```javascript
(() => {
  const out = [];
  const allMsg = document.querySelectorAll('[data-ad-preview="message"], [data-ad-comet-preview="message"]');
  allMsg.forEach((el, i) => {
    const body = el.innerText.trim();
    if (body.length < 8) return;
    out.push({ i, body: body.slice(0, 500) });
  });
  return JSON.stringify(out);
})()
```

每捲 10 ticks 跑一次，去重後累積。

### 踩過的雷

- `[role="article"]` 同時 match 貼文與留言，不能單用
- FB class names 已 obfuscated，不可靠
- 時間戳被 obfuscate 成亂碼，但正文乾淨

### Fallback

JS 拿空陣列 → 改用 `get_page_text(tabId)` 整段抓，用使用者名字當分界。

---

## 4. 目標數

**≥ 10-20 篇乾淨貼文**。若載不到，以實際收到的數量為準，在 `style_profile.md` 註明。

新 Page 可能 < 10 篇，沒關係，仍可 bootstrap voice profile（但要明確標示樣本數限制）。

---

## 5. 分析面向

**不只看字頻**，要萃取以下：

| 面向 | 關鍵提問 |
|---|---|
| 既有 voice | 是 jargon-heavy 顧問腔？口語創作者？翻譯員？混搭？ |
| 句長 | 短/中/長？混搭比例？ |
| 標點 | 驚嘆號頻率？全/半形？省略號樣式？ |
| 開頭 | 公告/時間錨/反思/情緒爆點？ |
| 收尾 | 結論型 / 問句 / 召集？ |
| Emoji | 頻率、偏好組合 |
| 主題 | 比例（AI / 半導體 / 資安 / 其他）|
| Hashtag | 用/不用 |
| 人稱 | 我/我們/企業 |
| **既有 jargon** | NPI / TCO / PDCA 等出現幾次 → 翻譯員轉換時要替換 |
| **hands-on 證據** | 是否提到自己 GitHub repo / 實作經驗 → 翻譯員 leverage |

---

## 6. 翻譯員轉換（**重要：本 skill 與 hao 原版的差異點**）

如果使用者既有 voice 是「**MBA 顧問腔**」（jargon-heavy / 戰略意涵 / 多維度的⋯），不能直接寫進 `style_profile.md`。要：

1. **保留戰略骨架**（影響 / 成本 / 時機 / 競爭）
2. **替換語言層**：用 `translation_dict.md` 把 9 個 jargon 詞換掉
3. **加入 hands-on 錨點**（從 GitHub / 實作經驗萃取）
4. **改成三模式 voice**（一三五 tools / 二四六 news / 日 recap）

**轉換前 vs 轉換後對照範例**：

> ❌ 原 voice（MBA 顧問腔）：
> 「當前生成式 AI 對算力的指數級挑戰，迫使我們重新審視底層硬體的極限。從戰略高度審視，這場技術革新是對產業競爭格局的結構性重塑⋯⋯」

> ✅ 翻譯員轉換後：
> 「**最近 AI 模型耗的算力越來越誇張**，連帶讓底層硬體（晶片、傳輸線）撐不住。從經營管理的角度看，這不只是性能問題，是整個產業要重新洗牌的訊號⋯⋯」

字數仍是 350-400 / 800-1000 區間，但 jargon 全替換成翻譯版。

---

## 7. 寫入 `style_profile.md`

完整覆寫，但**保留 `<!-- user-edit -->` 以下的使用者自訂段**。

格式參考 `style_profile.example.md`：
- 一句話語氣
- 三模式（一三五 / 二四六 / 日）
- 句式、標點、開頭、收尾、emoji、hashtag、人稱
- Voice Lock 8 條（快查版）
- 招牌詞庫（翻譯版）
- 範例（A 系列 + C 系列各一篇）
- 受眾畫像
- 身分線索 + hands-on 證據

---

## 8. 給使用者 3 條確認重點

用 3 個 bullet 摘要最顯著特徵，問「準嗎？哪裡歪了直接說。」

- 「準」→ 結束
- 「A 不對應該是 B」→ 對應段落改寫再存
- 「樣本太舊」→ 建議重抓

---

## 9. （新 Page 例外）若樣本 < 10 篇

直接跟使用者說：
> 你 Page 樣本不足（< 10 篇），無法穩定 bootstrap。建議：
> 1. 我用範本檔的 voice 結構（B2B / 翻譯員）寫一個 starter `style_profile.md`
> 2. 你發 10 篇後再跑「**重新學風格**」校正

不要假裝從 3 篇樣本萃取出穩定 voice。

---

## 開發紀錄

- v0.1 — 翻譯員轉換流程 + 新 Page 例外處理
