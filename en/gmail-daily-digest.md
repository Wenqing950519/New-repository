# Module: Gmail Daily Digest

## Overview

Scans your Gmail inbox every morning and sends a structured list of unread email subjects to Telegram. Only subjects are retrieved — no email body is read — leaving you to decide which emails to follow up on.

> ⚠️ Before using this module, complete the installation and authorization steps in `setup/gog-setup.md`.
> The `--services` flag must include `gmail`.

---

## Module Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `ENABLE_GMAIL` | Enable this module | `false` |
| `GMAIL_CRON` | Trigger schedule (UTC cron) | `0 23 * * *` |
| `MAX_RESULTS` | Maximum number of emails to scan per run | `20` |

---

## Trigger Conditions

### Scheduled (Auto)
- **Default time:** 07:00 in your local timezone (based on `GOG_TIMEZONE`)
- **Cron example (Asia/Taipei, UTC+8):** `0 23 * * *` (07:00 Taipei = 23:00 UTC)
- Adjust the schedule via `GMAIL_CRON`

### Manual Keywords
- "what emails do I have today"
- "check inbox"
- "unread emails"
- "email digest"
- "mail summary"

---

## Execution Steps

### Step 1: Scan important emails (primary)

```bash
gog gmail search 'is:unread category:primary' --max $MAX_RESULTS --json
```

### Step 2: Scan subscription notifications (updates)

```bash
gog gmail search 'is:unread category:updates' --max $MAX_RESULTS --json
```

### Step 3: Parse subjects

From both search results, extract:
- `subject` — email subject line
- `id` — thread ID

### Step 4: Format Telegram message

```
📬 Unread Emails · [MM/DD Weekday]

📌 Important
• [subject]
• [subject]

────────────────
📰 Newsletters & Updates
• [subject]
• [subject]

[N] unread — let me know which ones you'd like to read ✉️
```

Example output:
```
📬 Unread Emails · 03/12 Wed

📌 Important
• Your API usage summary for February
• Re: Meeting rescheduled to next week
• [openclaw/openclaw] PR #1234 merged

────────────────
📰 Newsletters & Updates
• Crypto Research: BTC Dominance hits 60%
• The Diff: Why AI companies keep losing money
• The Fed just blinked

9 unread — let me know which ones you'd like to read ✉️
```

If there are no unread emails:
```
📬 Unread Emails · [MM/DD Weekday]

Inbox is clear — nothing unread today ✨
```

> If either the primary or updates section has no results, omit that section and its divider.

### Step 5: Send to Telegram

Send the formatted message to the configured Telegram group or chat using the standard notification method.

---

## Error Handling

| Situation | Action |
|-----------|--------|
| `gog` command fails / auth error | Send: `⚠️ Could not fetch emails. Please check your Google authorization.` |
| JSON parse error | Send: `⚠️ Failed to parse email data. Please try again later.` |
| No unread emails | Send the "inbox is clear" message (see Step 4) |

---

## Notes

- Only `subject` is retrieved — email body is never read
- Raw JSON or command output must not appear in the Telegram message
