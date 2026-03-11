---
name: morning-briefing
description: >
  一套模組化的晨間簡報 skill，每天早上透過 Telegram 發送每日摘要。
  包含 Gmail 收件匣摘要與 Google Calendar 行程提醒兩個選用模組。
  當用戶想設定每日晨間簡報、安裝本 skill、或啟用/停用個別模組時使用。
  觸發關鍵字：「設定每日簡報」、「morning briefing」、「安裝晨間通知」、「啟用每日行程」、「啟用信件摘要」。
  首次安裝時，引導用戶完成設定與模組選擇。
version: 1.1.0
metadata:
  openclaw:
    requires:
      env:
        - GOG_KEYRING_PASSWORD
        - GOG_ACCOUNT
        - GOG_TIMEZONE
      bins:
        - gog
    primaryEnv: GOG_KEYRING_PASSWORD
    emoji: "🌅"
    homepage: https://github.com/Wenqing950519/gog-daily-remind
---

# SKILL：Morning Briefing 晨間簡報套件

## 概述

每天早上透過 Telegram 發送結構化的晨間簡報，包含信件摘要與行程提醒。
採模組化設計，用戶可自由選擇啟用哪些功能。各模組獨立運作，互不影響。

```
morning-briefing/
├── SKILL.md                              ← 主入口（本文件）
├── setup/
│   └── gog-setup.md                      ← 必要，gogcli 安裝與授權
└── modules/
    ├── gmail-daily-digest.md             ← 選用，每日信件摘要
    └── google-calendar-daily-brief.md    ← 選用，每日行程提醒
```

---

## 可用模組

| 模組 | 功能 | 預設觸發時間 | Cron 變數 |
|------|------|-------------|-----------|
| `gmail-daily-digest` | 掃描未讀信件，列出標題 | 07:00 | `GMAIL_CRON` |
| `google-calendar-daily-brief` | 掃描今日行程，整理成清單 | 07:00 | `CALENDAR_CRON` |

> 預設時間可透過 `GMAIL_CRON` / `CALENDAR_CRON` 調整。
> 各模組的完整變數說明請參考對應模組文件。

---

## 首次安裝流程

### Step 1：詢問用戶要啟用哪些模組

主動詢問用戶：

```
Morning Briefing 套件包含以下模組，請問你要啟用哪些？

1. 📬 Gmail 每日信件摘要（預設 07:00）
2. 🗓 Google Calendar 行程提醒（預設 07:00）
3. 兩個都要

請回覆編號。
```

依用戶回覆設定對應的環境變數：
- 選 1 或 3：`ENABLE_GMAIL=true`
- 選 2 或 3：`ENABLE_CALENDAR=true`

### Step 2：引導完成 gogcli 安裝

告知用戶：

```
在開始設定前，請先完成 gogcli 的安裝與授權。
請參考 setup/gog-setup.md，完成後告訴我繼續。
```

依用戶啟用的模組，提示需要授權的服務：
- 僅 Gmail：`--services gmail`
- 僅 Calendar：`--services calendar`
- 兩個都啟用：`--services gmail,calendar`

等待用戶確認完成後再進行下一步。

### Step 3：收集必要變數

依序詢問用戶以下資訊：

```
請告訴我：
1. 你的 GOG_KEYRING_PASSWORD（gogcli keyring 密碼）
2. 你的 GOG_ACCOUNT（Google 帳號 Email）
3. 你的時區（例如 Asia/Taipei、America/New_York）
```

### Step 4：載入對應模組

依用戶啟用的模組，載入對應文件：
- `ENABLE_GMAIL=true` → 載入 `modules/gmail-daily-digest.md`
- `ENABLE_CALENDAR=true` → 載入 `modules/google-calendar-daily-brief.md`

### Step 5：確認設定完成

發送確認訊息到 Telegram：

```
🌅 Morning Briefing 設定完成！

已啟用模組：
📬 Gmail 每日信件摘要 — 每天 07:00
🗓 行程提醒 — 每天 07:00

有問題隨時告訴我。
```

---

## 模組管理

### 啟用模組
```
請啟用 gmail-daily-digest
請啟用 google-calendar-daily-brief
```

### 停用模組
```
請停用 gmail-daily-digest
請停用 google-calendar-daily-brief
```

### 查看目前狀態
```
morning briefing 狀態
目前啟用了哪些模組
```

---

## 全域變數

以下變數為所有模組共用：

| 變數 | 說明 | 必填 |
|------|------|------|
| `GOG_KEYRING_PASSWORD` | gogcli keyring 密碼 | ✅ |
| `GOG_ACCOUNT` | Google 帳號 Email | ✅ |
| `GOG_TIMEZONE` | 用戶所在時區（影響時間顯示與 Cron 換算）| ✅ |
| `ENABLE_GMAIL` | 啟用信件模組 | 預設 `false` |
| `ENABLE_CALENDAR` | 啟用行程模組 | 預設 `false` |

> 各模組的專屬變數（如 `GMAIL_CRON`、`MAX_RESULTS`）請參考對應模組文件。

---

## 相關文件

- `setup/gog-setup.md` — gogcli 安裝與授權流程
- `modules/gmail-daily-digest.md` — Gmail 模組詳細說明
- `modules/google-calendar-daily-brief.md` — Calendar 模組詳細說明
