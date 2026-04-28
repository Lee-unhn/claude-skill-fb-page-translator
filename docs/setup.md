# Setup Walkthrough — fb-page-translator

從 0 到第一篇貼文發布的完整步驟。預期時間：30-60 分鐘（含 routine 設定）。

---

## Prerequisites Check

開始前確認：

- [ ] [Claude Code](https://claude.com/claude-code) 已安裝
- [ ] [Claude in Chrome MCP](https://docs.claude.com/en/docs/claude-code/mcp) 已安裝（在 Chrome extensions）
- [ ] Chrome 已登入你要發文的 FB Page（管理員權限）
- [ ] [claude.ai](https://claude.ai) 帳號已啟用 Routines / Scheduled Tasks
- [ ] 你的 GitHub 有公開 repo（用作 hands-on 素材，建議 ≥ 5 個）

---

## Step 1：Install skill

```bash
# Clone
git clone https://github.com/<YOUR_GITHUB_USERNAME>/claude-skill-fb-page-translator.git

# Copy skill folder
# macOS / Linux:
cp -r claude-skill-fb-page-translator/fb-page-translator ~/.claude/skills/fb-page-translator

# Windows (PowerShell):
Copy-Item -Path "claude-skill-fb-page-translator\fb-page-translator" -Destination "$env:USERPROFILE\.claude\skills\fb-page-translator" -Recurse

# Rename example files
cd ~/.claude/skills/fb-page-translator
mv style_profile.example.md style_profile.md
mv content_plan.example.md content_plan.md
```

---

## Step 2：Learn your voice

開 Claude Code，跟它說：

```
幫我學 FB 風格
```

Claude 會：
1. 問你 FB Page 網址
2. 用 Chrome MCP 開 Page、捲動、抓 ≥ 10 篇貼文
3. 分析 voice（既有風格 / jargon 用量 / 開頭 / 收尾 / emoji / 主題 / 人稱）
4. **若你既有 voice 是 MBA 顧問腔，自動轉換為翻譯員版本**（替換 9 個 jargon 詞）
5. 寫入 `~/.claude/skills/fb-page-translator/style_profile.md`
6. 給你 3 條確認重點

確認 OK 後進下一步。

⚠️ **新 Page 例外**：樣本 < 10 篇時 Claude 會說無法穩定 bootstrap，建議用範本起手 → 發 10 篇後重抓。

---

## Step 3：Plan your 30-day calendar

```
排三模式日曆
```

Claude 會：
1. 問你目標（漲粉 / 商業轉化 / thought leadership 累積）
2. 問你開始日期（建議下週一開始）
3. 寫入 30 天日曆到 `content_plan.md`
4. 一三五 = F-TOOL-DIVE / 二四六 = F-DAILY-NEWS / 日 = F-WEEK-RECAP

---

## Step 4：Set up 3 cloud routines

```
建立三個 routines
```

Claude 會 walk through 建立 3 個雲端 routine（用 `/schedule` skill）：

| Routine | Cron（UTC）| 說明 |
|---|---|---|
| `social-post-tools-dive` | `30 12 * * 1,3,5` | 一三五 20:30 台北 |
| `social-post-news-daily` | `30 12 * * 2,4,6` | 二四六 20:30 台北 |
| `social-post-sunday-trends` | `30 12 * * 0` | 週日 20:30 台北 |

每個 routine 的 prompt 會 inline：
- 任務流程（WebSearch / 篩選 / 套公式 / Voice Lock）
- 對應公式範本
- Voice Lock 8 條
- 你 voice 範本（從 `style_profile.md` 取）
- 你的 hands-on 背景（脫敏版）

⚠️ 你的姓名 / Email / 學歷 / 公司 **不要** inline 到 routine prompt（雲端 agent 會印出在 routine response 中、其他人可能看到）。用通用稱呼「**有經營管理經驗的人**」「**做過 [某行業]**」。

---

## Step 5：First post (Day 1)

設好 routine 後，**等明天 20:30** routine 自動觸發。

或者你也可以**現在手動跑一次**測試：

```
今天發一篇 [F-DAILY-NEWS / F-TOOL-DIVE] 測試
```

Claude 會：
1. 跑 WebSearch 蒐當天題材
2. 套對應公式生 350-400 字
3. 跑 Voice Lock 8 條
4. 顯示草稿 + 自我檢查
5. 等你回「**確認**」
6. 用 Chrome MCP 開 FB Page → 「在想些什麼？」（**不是頂部便利貼**）→ 貼文 → 發布
7. 寫戰績到 `content_plan.md`

---

## Step 6：Daily ritual (從 Day 2 起)

每天 21:00 你回家：

1. 開 Claude Code
2. 訪問 [claude.ai/code/routines](https://claude.ai/code/routines) 看今天 routine 草稿
3. 對 Claude 說：
   ```
   發今天的 daily
   ```
   或：
   ```
   發今天的 tools
   ```
   或（週日）：
   ```
   發本週 recap
   ```
4. Claude 接手讀草稿、跑最後一次 Voice Lock、等你「**確認**」
5. 你回「確認」 → 自動發布

每天約 30-60 秒。

---

## Step 7：14 天 review

第 14 天跑：

```
review 過去 14 天戰績
```

Claude 會：
1. 讀 `content_plan.md` 戰績欄位
2. 算平均觸及 / 互動率 / 鐵粉率
3. 找表現最好 / 最差的公式
4. 提出調整建議

---

## Step 8：30 天 Phase 1 終點

```
review Phase 1 終點
```

Claude 會：
1. 30 篇實戰戰績總結
2. 是否達到 Phase 2 啟動門檻（建議 ≥ 50 粉）
3. 字數策略校正建議
4. 自動化等級升級評估

---

## Troubleshooting

### Routine 沒自動觸發

- 檢查 routine 在 [claude.ai/code/routines](https://claude.ai/code/routines) 是否 `enabled: true`
- 檢查 cron 是否正確（時區用 UTC）

### Claude 找不到 FB「在想些什麼？」按鈕

- FB DOM 又變了 → 用 `read_page(filter="interactive")` 找
- 或截圖 + 視覺座標 fallback
- 詳見 `references/facebook_publishing.md`

### 草稿 Voice Lock 8 條 fail

- 通常是字數超過 / jargon 沒清乾淨 / 收尾用問句
- Claude 應自動重生成
- 如果 Claude 反覆 fail，跟它說具體哪一條 fail

### 想暫停某天

- 進 [claude.ai/code/routines](https://claude.ai/code/routines)
- 把對應 routine `disable`
- 隔天再 `enable`

### 想換成全自動（L4 / L5）

詳見 `references/routine_setup.md` 的「升級到 L4 / L5」章節。

---

## What's next

- 持續發 30 天累積戰績
- 第 14 / 30 天 review
- 達到 Phase 2 門檻後，混入月度深度文
- 達到 Phase 3 門檻後，跨平台同步（IG / Threads / LinkedIn）

⭐ 如果這個 skill 幫到你，回 GitHub repo 按個 Star，讓更多 B2B Page 經營者看到。
