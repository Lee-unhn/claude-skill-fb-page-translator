# fb-page-translator

> A [Claude Code](https://claude.com/claude-code) skill for B2B / thought-leadership FB Page operators who want **MBA-level strategic content in plain Chinese** — not jargon-heavy LinkedIn essays, not viral creator hype.

Inspired by and partially derived from [Hao0321/claude-skill-social-post](https://github.com/Hao0321/claude-skill-social-post). This skill diverges significantly in voice, schedule, and automation pattern.

---

## What problem does this solve

Most FB Page content tooling falls into two camps:

1. **Viral creator**（殺瘋了 / 草 / ！！！ / emoji 滿屏）— good for individual creators with personality-driven brands, terrible for B2B Pages targeting managers and decision-makers.
2. **MBA / Consultant**（NPI / TCO / PDCA / 結構性重塑）— signals expertise but kills readability. New Pages（< 100 fans）發 600-字 jargon 文 = 0 完讀率 = 演算法降權 = 死亡螺旋。

This skill is for the **third path**: 「**翻譯員**」voice. Take strategic intent (impact, cost, timing) but strip the jargon. Talk to a small business owner like you'd brief them in person — short sentences, concrete examples, hands-on credibility.

---

## Who is this for

- You run a FB **粉絲專頁** (Page), not a personal profile
- Your target reader is a **manager / decision-maker / business owner**, not a fellow developer
- You **have hands-on AI / tech experience** to ground your commentary（GitHub repos / 自己跑過工具 / 真實做過業務 — 這些 hands-on 信號是翻譯員 voice 的可信度核心）
- You want to **publish 1 post/day** without burning out
- You're OK with **a 30-second daily ritual**: open Claude Code at 21:00, review draft, confirm, post

If you're trying to grow a personal creator profile with viral content, use [Hao's original `social-post`](https://github.com/Hao0321/claude-skill-social-post) instead.

---

## Core concept: 3-mode schedule

| Day | Formula | Word count | Theme |
|---|---|---|---|
| **Mon / Wed / Fri** | F-TOOL-DIVE | 350-400 | AI tool deep-dive (hands-on insight from your GitHub repos or recent tools) |
| **Tue / Thu / Sat** | F-DAILY-NEWS | 350-400 | Past 24-30h AI news, translated for managers |
| **Sun** | F-WEEK-RECAP | **800-1000** | This week's 5-7 AI happenings, weekly digest |

All posts at 21:00 local time. Cloud routine drafts at 20:30 → you confirm at 21:00 → auto-publish via Chrome MCP.

---

## Quickstart

### Prerequisites

- [Claude Code](https://claude.com/claude-code)（macOS / Windows / Linux）
- [Claude in Chrome MCP](https://docs.claude.com/en/docs/claude-code/mcp) installed
- Chrome logged into the FB Page you want to publish to
- A claude.ai account with [Routines / Scheduled Tasks](https://claude.ai/code/routines) enabled
- Your Page should have **at least 10-20 public posts** OR you should be ready to bootstrap voice from scratch

### Install

```bash
# 1. clone
git clone https://github.com/<YOUR_GITHUB_USERNAME>/claude-skill-fb-page-translator.git

# 2. copy skill folder to Claude skills directory
# macOS / Linux:
cp -r claude-skill-fb-page-translator/fb-page-translator ~/.claude/skills/fb-page-translator
# Windows (PowerShell):
Copy-Item -Path "claude-skill-fb-page-translator\fb-page-translator" -Destination "$env:USERPROFILE\.claude\skills\fb-page-translator" -Recurse

# 3. rename example files (Claude will overwrite them with your version)
cd ~/.claude/skills/fb-page-translator
mv style_profile.example.md style_profile.md
mv content_plan.example.md content_plan.md
```

### Usage

Launch Claude Code and say:

```
幫我學 FB 風格
```
→ Claude opens Chrome, scrapes your FB Page's last 20 posts, writes a `style_profile.md` translator version.

```
排 30 天三模式日曆
```
→ Generates `content_plan.md` with Mon/Wed/Fri tools + Tue/Thu/Sat news + Sun recap.

```
建立三個 routines
```
→ Walks through creating the 3 cloud routines (see [docs/setup.md](docs/setup.md)).

Then daily:
```
發今天的貼文
```
→ Reads today's routine draft → re-runs Voice Lock 8-check → asks for "確認" → publishes via Chrome MCP.

For the full walkthrough: [`docs/setup.md`](docs/setup.md)

---

## Voice Lock 8 checklist (mandatory pre-publish)

Every draft must pass:

1. Word count: A-series 350-400 / C-series 800-1000
2. Paragraph count: A-series 4 段 / C-series 5-7 段 + sources
3. Para 1: bold subject anchor + strategic hook
4. Strategy paragraph contains 「**從經營管理的角度看**」 (or equivalent)
5. **No jargon**: NPI / TCO / PDCA / 數位韌性 / 結構性 / 良率預測 / 多維度 / 異質整合 / 顛覆性 — all banned
6. emoji = 0
7. Exclamation marks ≤ 1 (preferably 0)
8. Conclusion-style ending — no questions, no comment-bait, no hashtags

Any ❌ → regenerate.

---

## License

[MIT](LICENSE) — modify, use, sell freely. Just keep the LICENSE file.

This skill is derived from [Hao0321/claude-skill-social-post](https://github.com/Hao0321/claude-skill-social-post). 致謝 駱君昊 (Hao) 開了路.

---

## Contributing

PRs welcome, especially for:
- FB Page DOM changes (the publish flow breaks every few months)
- New formulas for adjacent verticals (B2B SaaS / consulting / academia / law-tech)
- More translation pairs for the jargon dictionary
- Localization (English / Japanese / Korean Page voice variants)

⭐ Star the repo if this saved you time. 覺得有用按星，讓更多 B2B Page 經營者看到。
