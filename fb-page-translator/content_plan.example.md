# 30 天內容日曆（範例）— 翻譯 AI 給老闆聽 Page

> ⚠️ **這是虛構示範檔**。第一次跟 Claude 說「排三模式日曆」，它會依你的 Page 狀態與目標覆寫此檔。
>
> 虛構角色：王俊賢 Page「翻譯 AI 給老闆聽」（對應 `style_profile.example.md`）

最後更新：範例

---

## 🎯 v0.1 排程結構

| 星期 | 公式 | 字數 | 主題類型 |
|---|---|---|---|
| 一三五 | F-TOOL-DIVE | 350-400 | AI 工具探討 |
| 二四六 | F-DAILY-NEWS | 350-400 | AI 新聞翻譯員短評 |
| 日 | F-WEEK-RECAP | **800-1000** | 本週 5-7 條 AI 大事翻譯員深度週報 |

---

## 30 天日曆（YYYY-MM-DD ~ YYYY-MM-DD）

| Day | 日期 | 星期 | 公式 | 主題（草稿題目）| 戰績 |
|---|---|---|---|---|---|
| 1 | YYYY-MM-DD | Mon | F-TOOL-DIVE | NotebookLM 整理產業報告（hands-on）| 待發 |
| 2 | YYYY-MM-DD | Tue | F-DAILY-NEWS | （routine 自動挑）| 待發 |
| 3 | YYYY-MM-DD | Wed | F-TOOL-DIVE | （routine 自動挑）| 待發 |
| 4 | YYYY-MM-DD | Thu | F-DAILY-NEWS | （routine 自動挑）| 待發 |
| 5 | YYYY-MM-DD | Fri | F-TOOL-DIVE | （routine 自動挑）| 待發 |
| 6 | YYYY-MM-DD | Sat | F-DAILY-NEWS | （routine 自動挑）| 待發 |
| **7** | **YYYY-MM-DD** | **Sun** | **F-WEEK-RECAP** | **本週 AI 大事**（首篇 800-1000 recap）| 待發 |
| 8 | YYYY-MM-DD | Mon | F-TOOL-DIVE | （routine 自動挑）| 待發 |
| 9 | YYYY-MM-DD | Tue | F-DAILY-NEWS | （routine 自動挑）| 待發 |
| 10 | YYYY-MM-DD | Wed | F-TOOL-DIVE | （routine 自動挑）| 待發 |
| 11 | YYYY-MM-DD | Thu | F-DAILY-NEWS | （routine 自動挑）| 待發 |
| 12 | YYYY-MM-DD | Fri | F-TOOL-DIVE | （routine 自動挑）| 待發 |
| 13 | YYYY-MM-DD | Sat | F-DAILY-NEWS | （routine 自動挑）| 待發 |
| **14** | **YYYY-MM-DD** | **Sun** | **F-WEEK-RECAP** | **本週 AI 大事 + 14 天 review checkpoint** | 待發 |
| 15 | YYYY-MM-DD | Mon | F-TOOL-DIVE | （routine 自動挑）| 待發 |
| 16-29 | ⋯ | ⋯ | ⋯ | （依排程輪替）| 待發 |
| **30** | **YYYY-MM-DD** | **Tue** | **F-DAILY-NEWS** | **+ Phase 1 終點 review** | 待發 |

**30 天分布**（按表）：
- F-TOOL-DIVE：13 天（一三五）
- F-DAILY-NEWS：13 天（二四六）
- F-WEEK-RECAP：4 天（日）

---

## 🤖 自動化設定（L3.5）

### 三個 routines

| Routine 名稱 | Cron（UTC）| Cron 解讀 | 公式 |
|---|---|---|---|
| `social-post-tools-dive` | `30 12 * * 1,3,5` | 一三五 20:30 台北 | F-TOOL-DIVE |
| `social-post-news-daily` | `30 12 * * 2,4,6` | 二四六 20:30 台北 | F-DAILY-NEWS |
| `social-post-sunday-trends` | `30 12 * * 0` | 週日 20:30 台北 | F-WEEK-RECAP |

詳細設定見 `references/routine_setup.md`。

### 每日執行流程

1. **20:30** 雲端 routine 觸發 → WebSearch 抓題材 → 套對應公式 → 跑 Voice Lock → 草稿輸出
2. **21:00** 你回家 → 開本機 Claude Code → 訪問 [claude.ai/code/routines](https://claude.ai/code/routines) 看草稿
3. 你說「**發今天的貼文**」（或具體：「發今天的 daily」/「發今天的 tools」/「發本週 recap」）
4. 本機 social-post skill 接手 → 讀草稿 → 跑最後一次 Voice Lock → 等你回「**確認**」
5. 你回「**確認**」 → Chrome MCP 開 FB Page → 貼文 → 發布
6. 2h / 24h / 48h 後自動回查觸及寫進此檔戰績欄位

### R8 安全閘

開源版 SKILL.md 規定發前必須拿到使用者明確「確認」字眼。**不可 bypass**。

---

## 主題冷卻紀錄（R5 鐵則）

**4 天內主題不重複**。

| 主題 | 最近發布日 | 4 天冷卻到 |
|---|---|---|
| (空) | — | — |

---

## 戰績追蹤格式

每篇發完，update 該 row「戰績」欄位：

```
[發布時間] / [2h: 觸及/讚/留言/分享] / [24h: 同上] / [48h plateau: 同上 + 鐵粉率%] / 連結點擊率%
```

---

## Review checkpoints

### 14 天 review

- [ ] 14 篇平均觸及（基準）
- [ ] F-TOOL-DIVE / F-DAILY-NEWS / F-WEEK-RECAP 哪個公式表現最好
- [ ] 是否漲粉到目標
- [ ] 哪些主題有共鳴 / 哪些被忽略

### 30 天 Phase 1 終點 review

- [ ] 30 篇實戰戰績
- [ ] 是否達到 Phase 2 啟動門檻
- [ ] 字數策略校正（若週日 800-1000 字 plateau 觸及 < 平日，要往下修）
- [ ] L3.5 → L4 / L5 升級評估

---

## 受眾畫像（虛構，請依實際校正）

### 平日 A 系列

- 年齡 30-50 歲
- 身分：中小企業主 / 中階主管
- FB 活躍時段：晚間 21:00-22:00

### 週日 C 系列

- 同上但有空閒消化
- FB 活躍時段：週日晚 20:00-22:00

---

## 歷史日曆

（暫無）
