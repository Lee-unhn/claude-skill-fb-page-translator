# L3.5 雲端 Routine 設定教學

> 自動化等級 L3.5：雲端 routine 自動 WebSearch + 生稿，本機手動「確認」字眼後發。
> 開源版 R8 安全閘**不可 bypass**。需要全自動者請 Fork 私人版或改用 Meta Graph API。

---

## 自動化等級對照

| Level | 描述 | 工程量 | 失敗點 |
|---|---|---|---|
| **L3** | 全手動：你每天手動 WebSearch + 寫稿 + 發 | 0 | 你要記得每天發 |
| **L3.5（推薦）** | 雲端 routine 自動生稿，本機手動「確認」後發 | 中（建 3 個 routines）| R8 閘門無法 bypass |
| **L4** | Fork 私人版 + Windows 任務排程器 + 移除 R8 | 高 | 電腦睡眠 / Chrome 沒開 |
| **L5** | Meta Graph API 雲端直發 | 高（要申請 + 審核）| Meta 開發者帳號 |

---

## L3.5 架構

```
┌─ Anthropic Cloud（雲端 routine）──────────────┐
│                                                │
│  20:30 台北自動觸發（依星期挑公式）：          │
│   - 一三五 → F-TOOL-DIVE                       │
│   - 二四六 → F-DAILY-NEWS                      │
│   - 週日 → F-WEEK-RECAP                        │
│  ↳ WebSearch（agent / skill / mcp / claude /   │
│     vibe / notebooklm / openai 關鍵字）        │
│  ↳ 篩 1 條（A 系列）或 5-7 條（C 系列）        │
│  ↳ 套對應公式生 350-400 / 800-1000 字          │
│  ↳ Voice Lock 8 條                             │
│  ↳ 輸出草稿到 routine response                 │
│                                                │
└────────────────────────┬───────────────────────┘
                         │ 你 21:00 回家看
                         ▼
┌─ 你的本機 Claude Code ────────────────────────┐
│                                                │
│  你：「發今天的 [tools/daily/recap]」          │
│  ↳ skill 啟動                                  │
│  ↳ 讀 routine 草稿 + style_profile.md          │
│  ↳ 跑最後一次 Voice Lock 8 條                  │
│  ↳ 等你回「確認」字眼（R8 安全閘）             │
│  ↳ Chrome MCP 開 FB Page                       │
│  ↳ 點「在想些什麼？」→ 貼文 → 發布             │
│  ↳ 2h/24h/48h 回查觸及寫戰績                   │
│                                                │
└────────────────────────────────────────────────┘
```

---

## 設定 3 個 Routines

### 觸發 cron 對照（UTC）

| Routine 名稱 | Cron | 解讀 | 公式 |
|---|---|---|---|
| `social-post-tools-dive` | `30 12 * * 1,3,5` | 一三五 20:30 台北 | F-TOOL-DIVE |
| `social-post-news-daily` | `30 12 * * 2,4,6` | 二四六 20:30 台北 | F-DAILY-NEWS |
| `social-post-sunday-trends` | `30 12 * * 0` | 週日 20:30 台北 | F-WEEK-RECAP |

> **時區提醒**：cron 全用 UTC。台北 (UTC+8) 20:30 = UTC 12:30。其他時區依此換算。

### 建 routines 的方法

在 Claude Code 用 `/schedule` skill：

```
/schedule
```

或對話：
```
建立每週一三五 20:30 台北時間的 routine 叫 social-post-tools-dive
```

每個 routine 的 prompt 必須**完全自包含**（雲端 agent 無法讀本機檔案），inline 包含：
1. 任務流程（WebSearch / 篩選 / 套公式 / Voice Lock）
2. 對應公式範本（從 `formulas.md` 複製）
3. Voice Lock 8 條
4. Voice 範本（從 `style_profile.md` 取**已脫敏**的範例）
5. 使用者背景錨點（hands-on / 經驗）
6. 產出格式（shortlist + 草稿 + Voice Lock 自檢）

---

## Routine Prompt 範本：F-TOOL-DIVE

```
你是 [你的名字] 的 F-TOOL-DIVE 內容生成助理。每週一三五 20:30 台北（12:30 UTC）執行：產出當日「AI 工具探討」350-400 字翻譯員短文。

# 你的限制
- 跑在 Anthropic 雲端，無法操作使用者本機 Chrome / FB / 本機檔案
- 最終輸出 = 一段中文 350-400 字貼文草稿
- 不要嘗試「自動發布」或呼叫 FB API

# 任務流程

## Step 1：蒐題（並行 3-4 種來源）
A. WebSearch GitHub trending AI 工具
B. WebSearch 主流 AI 工具（Claude Code / Cursor / NotebookLM 等）過去 7 天更新
C. 讀使用者 GitHub repos（github.com/<YOUR_USERNAME>）找近期 commit

## Step 2：選 1 條最強題材
優先順序：使用者自己 repo > 使用者最近用過的工具 > GitHub trending > 主流廠商更新

## Step 3：套 F-TOOL-DIVE 結構生 350-400 字
[此處 inline F-TOOL-DIVE 結構，從 formulas.md 複製]

## Step 4：Voice Lock 8 條
[此處 inline Voice Lock 8 條]

# Voice 範本
[1-2 個從 style_profile.md 取的範例]

# 使用者背景
[你的脫敏背景：經營管理 / 技術 hands-on / 半年 AI 經驗等，避免具體姓名 / 學校 / 公司]

# 產出格式
1. shortlist 5-10 條
2. 草稿（350-400 字 + 資料來源）
3. Voice Lock 自我檢查表

# 重要提醒
- 發布權在使用者：你只生草稿，不發布
- 必須引用真實 URL
- 語氣 = 翻譯員（普通人版 MBA）
- 4 天內主題不重複（R5 鐵則）
```

### 同樣方法做 F-DAILY-NEWS 與 F-WEEK-RECAP

把上述範本的「Step 1 蒐題」「Step 3 套公式」「Voice 範本」改成對應公式的版本。F-WEEK-RECAP 字數改 800-1000、Step 2 改成「篩 5-7 條」、Step 1 改成「過去 7 天而非 24h」。

---

## 本機 Skill 接手流程（21:00）

當你 21:00 回家：

1. 開 Claude Code
2. 訪問 [claude.ai/code/routines](https://claude.ai/code/routines)
3. 找今天 routine 的 final response（草稿 + Voice Lock 自檢）
4. 對 Claude Code 說：
   ```
   發今天的 daily
   ```
   或：
   ```
   發今天的 tools
   ```
   或：
   ```
   發本週 recap
   ```
5. Claude 會：
   - 讀你貼上的草稿
   - 讀本機 `style_profile.md`
   - 跑最後一次 Voice Lock 8 條
   - 顯示草稿給你 review
   - 等你回「**確認**」
6. 你回「**確認**」 → Chrome MCP 開 FB Page → 貼文 → 發布
7. 發完 2h / 24h / 48h 自動回查觸及寫進 `content_plan.md` 戰績

---

## 跨 routine 衝突處理

### 衝突情境：今天已手動發過，routine 又自動跑

例：你週三上午手動發了一篇，但週三 20:30 routine 仍會自動觸發生稿。

**處理方式**：
- routine 仍會跑，但你 21:00 看到草稿後**直接忽略**（不執行「發今天的 tools」）
- 不會違反 R1 一天一篇鐵則（因為你沒按確認）

如果你想跳過特定一天的 routine：
- 進 [claude.ai/code/routines](https://claude.ai/code/routines)
- Disable 該 routine
- 隔天再 enable

---

## 升級到 L4（半自動）

需要時，步驟：

1. **Fork 此 repo 到你私人帳號**（變私有 repo）
2. **修改 SKILL.md**：移除 R8 確認字眼閘門
3. **修改本機 skill 安裝**：用你私人 fork
4. **設 Windows 任務排程器**（或 macOS launchd）：每天 20:55 自動啟動 Claude Code 並帶 prompt
5. Chrome 必須開機 + 登入 FB

⚠️ L4 失敗點：電腦睡眠 / Chrome 沒開 / Claude Code 啟動失敗 → 那天就沒發。

---

## 升級到 L5（全自動 Meta Graph API）

需要時，步驟：

1. 申請 [Meta for Developers](https://developers.facebook.com/) 帳號
2. 建立 App → 申請 Page Access Token
3. 通過 Meta App Review（內容審核 1-2 週）
4. 改寫雲端 routine：不用 Chrome MCP，直接 POST 到 `https://graph.facebook.com/v22.0/<PAGE_ID>/feed`

完全脫離本機 + Chrome 依賴，但要 Meta 開發者審核。

---

## 開發紀錄

- v0.1 — L3.5 三 routine 配置 + cron + 本機接手流程
