# 模組：Gmail 每日信件摘要

## 概述

每天早上自動掃描 Gmail 收件匣，將未讀信件標題整理成結構化清單發送到 Telegram。
只列標題，不讀內文，讓用戶自行決定是否深入查看。

> ⚠️ 使用本模組前請先完成 `setup/gog-setup.md` 的安裝與授權流程，
> 授權時 `--services` 需包含 `gmail`。

---

## 模組變數

| 變數 | 說明 | 預設值 |
|------|------|--------|
| `ENABLE_GMAIL` | 是否啟用本模組 | `false` |
| `GMAIL_CRON` | 觸發時間（UTC Cron） | `0 23 * * *` |
| `MAX_RESULTS` | 每次掃描最大信件數 | `20` |

---

## 觸發條件

### 定時自動觸發
- **預設時間：** 每天早上 07:00（依 `GOG_TIMEZONE` 換算）
- **Cron 範例（Asia/Taipei，UTC+8）：** `0 23 * * *`（台北 07:00 = UTC 23:00）
- 可透過 `GMAIL_CRON` 調整觸發時間

### 手動關鍵字觸發
- 「今天有什麼信」
- 「查信箱」
- 「未讀信件」
- 「email digest」
- 「信件摘要」

---

## 執行步驟

### Step 1：掃描重要信件（primary）

```bash
gog gmail search 'is:unread category:primary' --max $MAX_RESULTS --json
```

### Step 2：掃描訂閱通知（updates）

```bash
gog gmail search 'is:unread category:updates' --max $MAX_RESULTS --json
```

### Step 3：解析標題

從兩次搜尋結果中，每筆擷取：
- `subject` — 信件標題
- `id` — thread ID

### Step 4：格式化 Telegram 訊息

```
📬 今日未讀信件 · [MM/DD 週X]

📌 重要信件
• [標題]
• [標題]

────────────────
📰 訂閱通知
• [標題]
• [標題]

共 [N] 封未讀，有興趣的直接跟我說想看哪封 ✉️
```

範例輸出：
```
📬 今日未讀信件 · 03/12 週三

📌 重要信件
• Your API usage summary for February
• 關於下週會議的安排
• [openclaw/openclaw] PR #1234 merged

────────────────
📰 訂閱通知
• Crypto Research: BTC Dominance hits 60%
• The Diff: Why AI companies keep losing money
• The Fed just blinked

共 9 封未讀，有興趣的直接跟我說想看哪封 ✉️
```

若今天沒有任何未讀信件：
```
📬 今日未讀信件 · [MM/DD 週X]

今天沒有未讀信件，收件匣很乾淨 ✨
```

> 若 primary 或 updates 其中一個區塊沒有信件，省略該區塊與分隔線

### Step 5：發送到 Telegram

使用標準 Telegram 通知方式，將格式化訊息發送到指定群組或對話。

---

## 錯誤處理

| 情況 | 處理方式 |
|------|----------|
| `gog` 指令失敗 / 授權錯誤 | 發送：`⚠️ 無法取得信件，請檢查 Google 授權狀態。` |
| JSON 解析失敗 | 發送：`⚠️ 信件資料解析失敗，請稍後再試。` |
| 沒有任何未讀信件 | 發送「收件匣很乾淨」版本訊息（見 Step 4）|

---

## 注意事項

- 只擷取 subject，不讀取信件內文
- Telegram 訊息不得包含原始 JSON 或指令輸出
