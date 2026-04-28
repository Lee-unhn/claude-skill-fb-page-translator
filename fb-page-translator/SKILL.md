---
name: fb-page-translator
description: 為 B2B / Thought leadership FB 粉絲專頁產出「翻譯員」voice 內容（一三五工具探討 / 二四六新聞短評 / 日週度大事統整）。觸發詞：「發 FB 貼文」「發今天的 daily」「發今天的 tools」「發本週 recap」「學我 FB 風格」「排三模式日曆」「查 FB 流量」。
---

Base directory for this skill: `~/.claude/skills/fb-page-translator/`

# fb-page-translator skill

> 為 B2B FB Page 經營者設計的內容自動化 skill。三模式排程 + 翻譯員 voice + L3.5 雲端 routine。
> Repo: https://github.com/<YOUR_GITHUB_USERNAME>/claude-skill-fb-page-translator
> License: MIT
>
> Inspired by: [Hao0321/claude-skill-social-post](https://github.com/Hao0321/claude-skill-social-post)

---

## 階段路由

| 觸發 | 階段 | 讀 |
|---|---|---|
| `style_profile.md` 不存在 / 說「重新學風格」| **P1 學風格** | `references/learn_style.md` |
| `content_plan.md` 不存在 / 說「排三模式日曆」「重新規劃」| **P0 排日曆** | `references/phase_strategy.md` + `references/formulas.md` |
| 說「發今天的貼文」「發 daily」「發 tools」「發本週 recap」「PO」| **P2 生稿 + 發布** | `references/formulas.md`（對應公式段）+ `style_profile.md` + `content_plan.md` + `references/voice_lock.md` + `references/facebook_publishing.md` |
| 說「建立 routines」「設定排程」| **P3 routine 設定** | `references/routine_setup.md` |
| 說「查流量」「分析」「review」| **診斷** | `references/phase_strategy.md`（review checkpoints 段）|

路由前用一句話告知使用者要做哪階段，給糾正機會。

---

## 先決條件

- `mcp__Claude_in_Chrome__*` 可用（否則停、不模擬）
- 使用者已登入目標 FB Page（登入牆出現請使用者手動登）
- claude.ai 帳號已啟用 Routines / Scheduled Tasks（用於 L3.5 自動化）

---

## 🛡️ R8 安全閘（硬規則不可覆寫）

**每次實際發布前必須在對話裡拿到使用者明確「確認」字眼。** 沒「確認」不發。

開源版 SKILL.md **永遠保留此閘門不可 bypass**。需要全自動的人請：
1. Fork 變私人版本，自行修改 SKILL.md
2. 或改用 Meta Graph API（不在本 skill 範圍）

---

## 不要做

- 沒授權發文字眼就發
- 跨平台同一段複製（每平台需要重新適配 voice）
- 自動按讚 / 回覆 / follow / 大量留言
- 外傳 `style_profile.md` / 使用者資料
- 幫登入 / 改隱私 / 改帳號
- 猜測 FB 個人頁 / Page 網址（P1 必須問）
- 刪除使用者留言 / 貼文（系統硬規則）

---

## 📋 核心規則

### R1：一天一篇鐵則

24h 內最多 1 篇。多發 = 演算法判 spam → 觸及降到 30-50%。

### R2：爆款後 24h 冷卻

爆款（讚 ≥ 100 或讚 ≥ 50 且留言/讚 > 0.5），24h 內禁發深度文。回留言 + 拉群 + 不重複內容。

### R3：突破鐵粉圈（連續 3 篇 < 15% 非追蹤者）

演算法歸類為「鐵粉限定創作者」。下一篇必用廣推題材（外部 tag、跨界對比、產業具名）。

### R4：時段按受眾分流

B2B / 經營層級受眾 → 平日 **21:00**（晚餐後 review）+ 週日 **20:00-22:00**（為下週備課）。

### R5：主題冷卻硬規則

**同一「主題」4 天內不重複**。主題 = 標題或第一段核心 noun phrase（例：「OpenAI 模型升級」「MCP 標準化」「Cursor 新功能」）。違反成本：實證會把觸及壓到前一篇的 0.13%。

### R6：評估看 4 指標 + 必須 48-72h plateau 才判定

詳見 `references/phase_strategy.md`。

**早期判斷規則**：
- 1-6h 數據：完全不要下結論
- 6-24h 數據：只能說「目前趨勢」
- 48-72h plateau：才能用 4 指標判定

**4 指標**：
1. 連結點擊率 > 1%
2. 追蹤者比例 > 15%（突破鐵粉圈）
3. 互動率 > 5%
4. 下游轉化（留言質量 / 私訊 / 名單成長）

### R7：真正的 KPI 是長期商業價值，不是讚

讚 / 觸及只是表面。B2B Page 真 KPI：
- 私訊問詢數
- 名單表單 / Newsletter 訂閱
- 演講邀約 / 顧問詢價
- 個人品牌信任積累

評估每篇值不值得，看 downstream 而非眼前數字。

### R8：Voice Lock 8 條（**統一檢查表**，見 `references/voice_lock.md`）

任何草稿生成後必跑：

1. **字數**：A 系列 350-400；C 系列 800-1000
2. **段落**：A 系列 4 段；C 系列 5-7 段（含資料來源前的本文）
3. **第 1 段**：粗體開頭錨點（事件主角 / 工具名 / 週度框架）+ 戰略點題
4. **戰略段**：含「**從經營管理的角度看**」（或同義句）
5. **無 jargon**：避開 NPI / TCO / PDCA / 數位韌性 / 結構性 / 良率預測 / 多維度 / 異質整合 / 顛覆性 9 個詞（見 `references/translation_dict.md` 翻譯版）
6. **emoji = 0**
7. **驚嘆號 ≤ 1**（最好 = 0）
8. **收尾**：結論型，不問問題、不召集留言、無 hashtag

任一條 ❌ → 重生成，**不要交稿**。

---

## 🤖 自動化等級

| Level | 描述 | 工程量 | 失敗點 |
|---|---|---|---|
| **L3** | 全手動 | 0 | 你要記得每天發 |
| **L3.5（推薦預設）** | 雲端 routine 自動生稿，本機手動「確認」字眼後發 | 中（建 3 個 routines）| R8 閘門無法 bypass |
| **L4** | Fork 私人版 + Windows 任務排程器 + 移除 R8 閘門 | 高 | 電腦睡眠 / Chrome 沒開 |
| **L5** | Meta Graph API 雲端直發 | 高（要申請 + 審核）| 需要 Meta 開發者帳號 |

詳見 `references/routine_setup.md`。

---

## 💡 實用技巧

### 一三五 / 二四六 / 日 三模式分配邏輯

- **一三五 工具探討**（F-TOOL-DIVE）= hands-on 信號 + thought leadership 累積
- **二四六 新聞短評**（F-DAILY-NEWS）= 時效性 + 觸及陌生受眾
- **日 大事統整**（F-WEEK-RECAP）= 週度 SEO + 為下週話題備課

不是 7 天都一樣 → 訓練讀者預期 → 增加 retention 訊號 → 演算法判定為「有節奏的內容生產者」。

### FB「在想些什麼？」vs 便利貼

點 Page 頂部的「分享想法⋯⋯」氣泡 = FB 新便利貼（限 60 字），**不是貼文**。
真正貼文撰寫框是**捲下去**後的「**在想些什麼？**」+ 相片/Reel/直播選項那塊。

詳見 `references/facebook_publishing.md`。

### 留言框 Enter = 送出

FB / Threads 留言框 `\n` 會被當送出。**留言永遠壓成單行**用 `→` `，` `／` 分隔，不用換行（compose modal 才可多行）。

---

## 🔄 持續優化

- 14 天 review checkpoint
- 30 天 Phase 1 終點 review

詳見 `references/phase_strategy.md`。

---

## 📌 快速查詢

| 需要 | 去 |
|---|---|
| Voice Lock 8 條詳解 | `references/voice_lock.md` |
| 3 公式範本 + 範例 | `references/formulas.md` |
| Jargon 翻譯對照 | `references/translation_dict.md` |
| Phase 1/2/3 經營邏輯 | `references/phase_strategy.md` |
| L3.5 雲端 routine 設定 | `references/routine_setup.md` |
| Chrome MCP 發 FB 流程 | `references/facebook_publishing.md` |
| 風格學習方法 | `references/learn_style.md` |
