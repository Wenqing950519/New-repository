[English](#english) | [繁體中文](#繁體中文)

---

<a name="english"></a>

# 🌅 gog-daily-remind

> Gmail notifications are easy to miss. Every morning you still have to open Calendar to check what's happening today — this skill exists to fix that.
>
> Let your AI agent organize your emails and schedule before you wake up, delivered straight to your Telegram.

A modular OpenClaw skill that sends a structured morning briefing to Telegram every day. Built on [gogcli](https://github.com/steipete/gogcli) — a Google Workspace CLI for AI agents.

---

## Features

**📬 Gmail Daily Digest**
Scans your unread emails every morning and lists subjects by category — important emails and newsletters separated, no body content read. You decide what's worth opening.

**🗓 Google Calendar Brief**
Pulls today's events and formats them into a clean agenda — timed events sorted chronologically, all-day events grouped below.

Both modules deliver to Telegram at **07:00** by default, and each can be enabled or disabled independently.

---

## How It Looks

```
📬 Unread Emails · 03/12 Wed

📌 Important
• Your API usage summary for February
• Re: Meeting rescheduled to next week

────────────────
📰 Newsletters & Updates
• Crypto Research: BTC Dominance hits 60%
• The Diff: Why AI companies keep losing money

9 unread — let me know which ones you'd like to read ✉️
```

```
🗓 03/12 Wed

• 09:30 Team standup
  📍 Google Meet
  📝 Q2 planning discussion

• 14:00–15:30 Design review

────────────────
• Rent due

2 events today — have a great day ✨
```

---

## Architecture

```
gog-daily-remind/
├── en/                                   # English
│   ├── SKILL.md                          # Main entry point
│   ├── setup/
│   │   └── gog-setup.md                  # gogcli installation & auth
│   └── modules/
│       ├── gmail-daily-digest.md         # Gmail module
│       └── google-calendar-daily-brief.md
└── zh-TW/                                # Traditional Chinese
    ├── SKILL.md
    ├── setup/
    │   └── gog-setup.md
    └── modules/
        ├── gmail-daily-digest.md
        └── google-calendar-daily-brief.md
```

**SKILL.md** is the main entry point — it guides the user through setup and module selection on first install. Each module is self-contained and loads independently.

---

## Quick Start

1. Set up a Google Cloud project and create OAuth credentials — this is where most people get stuck. See [`en/setup/gog-setup.md`](en/setup/gog-setup.md) for the full walkthrough
2. Install [gogcli](https://github.com/steipete/gogcli) and complete OAuth authorization
3. Add this repo to your OpenClaw installation
4. Tell your agent: `set up morning briefing`
5. Follow the guided setup — choose your modules, provide your credentials, done

---

## Requirements

- [OpenClaw](https://github.com/openclaw/openclaw)
- [gogcli](https://github.com/steipete/gogcli)
- Google account with Calendar and/or Gmail access
- Telegram bot connected to your OpenClaw agent

---

<a name="繁體中文"></a>

# 🌅 gog-daily-remind

> Gmail 通知總是漏看，每天早上還得特地切到 Calendar 確認今天有什麼——這套 skill 就是為了解決這件事。
>
> 讓 AI agent 在你醒來之前就把信件和行程整理好，直接送到你的 Telegram。

一套模組化的 OpenClaw skill，每天早上透過 Telegram 發送結構化的晨間簡報。基於 [gogcli](https://github.com/steipete/gogcli)——專為 AI agent 設計的 Google Workspace CLI。

---

## 功能

**📬 Gmail 每日信件摘要**
每天早上掃描未讀信件，依分類列出標題——重要信件與訂閱通知分開顯示，不讀內文。你來決定哪封值得打開。

**🗓 Google Calendar 行程提醒**
抓取今日行程並整理成清單——有時間的行程依序排列，全天事項另外顯示。

兩個模組預設都在早上 **07:00** 發送，可以各自獨立啟用或停用。

---

## 輸出範例

```
📬 今日未讀信件 · 03/12 週三

📌 重要信件
• Your API usage summary for February
• 關於下週會議的安排

────────────────
📰 訂閱通知
• Crypto Research: BTC Dominance hits 60%
• The Diff: Why AI companies keep losing money

共 9 封未讀，有興趣的直接跟我說想看哪封 ✉️
```

```
🗓 03/12 週三

• 09:30 晨會
  📍 Google Meet
  📝 Q2 規劃討論

• 14:00–15:30 與設計師對稿

────────────────
• 繳房租截止日

共 2 個行程，祝今天順利 ✨
```

---

## 架構

```
gog-daily-remind/
├── en/                                   # 英文版
│   ├── SKILL.md                          # 主入口
│   ├── setup/
│   │   └── gog-setup.md                  # gogcli 安裝與授權
│   └── modules/
│       ├── gmail-daily-digest.md         # Gmail 模組
│       └── google-calendar-daily-brief.md
└── zh-TW/                                # 繁體中文版
    ├── SKILL.md
    ├── setup/
    │   └── gog-setup.md
    └── modules/
        ├── gmail-daily-digest.md
        └── google-calendar-daily-brief.md
```

**SKILL.md** 是主入口，首次安裝時引導用戶完成設定與模組選擇。每個模組各自獨立，互不影響。

---

## 快速開始

1. 建立 Google Cloud 專案並取得 OAuth 憑證——這是最多人卡關的一步，完整流程請參考 [`zh-TW/setup/gog-setup.md`](zh-TW/setup/gog-setup.md)
2. 安裝 [gogcli](https://github.com/steipete/gogcli) 並完成 OAuth 授權
3. 將本 repo 加入你的 OpenClaw
4. 告訴你的 agent：「設定每日簡報」
5. 跟著引導完成設定——選擇模組、填入帳號資訊，完成

---

## 環境需求

- [OpenClaw](https://github.com/openclaw/openclaw)
- [gogcli](https://github.com/steipete/gogcli)
- 有 Calendar 和/或 Gmail 權限的 Google 帳號
- 連接到你的 OpenClaw agent 的 Telegram bot
