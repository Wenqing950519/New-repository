# Module: Google Calendar Daily Brief

## Overview

Scans Google Calendar every morning and sends a structured agenda for today to Telegram.

> ⚠️ Before using this module, complete the installation and authorization steps in `setup/gog-setup.md`.
> The `--services` flag must include `calendar`.

---

## Module Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `ENABLE_CALENDAR` | Enable this module | `false` |
| `CALENDAR_CRON` | Trigger schedule (UTC cron) | `0 23 * * *` |
| `GOG_TIMEZONE` | User timezone (used to determine "today") | Required |

---

## Trigger Conditions

### Scheduled (Auto)
- **Default time:** 07:00 in your local timezone (based on `GOG_TIMEZONE`)
- **Cron example (Asia/Taipei, UTC+8):** `0 23 * * *`
- Adjust the schedule via `CALENDAR_CRON`

### Manual Keywords
- "what's on my calendar today"
- "check my schedule"
- "today's agenda"
- "calendar brief"
- "any events today"

---

## Execution Steps

### Step 1: Fetch today's events

```bash
gog calendar events --today --json
```

### Step 2: Parse and categorize events

From the `events` array in the JSON response, extract the following fields per event:

| Field | Description |
|-------|-------------|
| `summary` | Event title |
| `start.dateTime` / `start.date` | Start time (timed vs. all-day) |
| `end.dateTime` / `end.date` | End time |
| `startLocal` / `endLocal` | Local time string (use when available) |
| `description` | Notes (only display if non-empty) |
| `location` | Location (only display if non-empty) |

Categorization:
- **Timed events** — have `start.dateTime`, sorted by `startLocal`, displayed first
- **All-day events** — have only `start.date`, displayed below a divider

### Step 3: Format Telegram message

```
🗓 [MM/DD Weekday]

• [HH:MM] [event title]
  📍 [location]
  📝 [notes]

• [HH:MM–HH:MM] [event title]

────────────────
• [all-day event title]
• [all-day event title]

[N] events today — have a great day ✨
```

Example output:
```
🗓 03/12 Wed

• 09:30 Team standup
  📍 Google Meet
  📝 Q2 planning discussion

• 14:00–15:30 Design review

────────────────
• Rent due
• Mom's birthday

4 events today — have a great day ✨
```

If there are no events today:
```
🗓 [MM/DD Weekday]

Nothing scheduled today — enjoy the free time 🌿
```

> Omit empty fields (no location = skip the 📍 line)
> If there are no all-day events, omit the divider and that section entirely

### Step 4: Send to Telegram

Send the formatted message to the configured Telegram group or chat using the standard notification method.

---

## Error Handling

| Situation | Action |
|-----------|--------|
| `gog` command fails / auth error | Send: `⚠️ Could not fetch calendar. Please check your Google authorization.` |
| JSON parse error | Send: `⚠️ Failed to parse calendar data. Please try again later.` |
| No events found | Send the "nothing scheduled" message (see Step 3) |

---

## Notes

- "Today" is determined using the `GOG_TIMEZONE` timezone
- Raw JSON or command output must not appear in the Telegram message
