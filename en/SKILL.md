---
name: morning-briefing
description: >
  A modular morning briefing skill that sends daily summaries to Telegram.
  Includes Gmail inbox digest and Google Calendar agenda as optional modules.
  Use when the user wants to set up a daily morning briefing, install this skill,
  or enable/disable individual modules (gmail-daily-digest, google-calendar-daily-brief).
  Triggers: "set up morning briefing", "daily briefing", "install morning notifications",
  "enable calendar reminders", "enable email digest".
  On first install, guide the user through setup and module selection.
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
    homepage: https://github.com/your-username/your-repo
---

# SKILL: Morning Briefing

## Overview

Delivers a structured morning briefing to Telegram every day, covering unread emails and today's calendar events. Modular by design — each module is independent and can be enabled or disabled without affecting the others.

```
morning-briefing/
├── SKILL.md                              ← Main entry point (this file)
├── setup/
│   └── gog-setup.md                      ← Required: gogcli installation & auth
└── modules/
    ├── gmail-daily-digest.md             ← Optional: daily email digest
    └── google-calendar-daily-brief.md    ← Optional: daily calendar agenda
```

---

## Available Modules

| Module | Function | Default Trigger | Cron Variable |
|--------|----------|-----------------|---------------|
| `gmail-daily-digest` | Scans unread emails and lists subjects | 07:00 | `GMAIL_CRON` |
| `google-calendar-daily-brief` | Scans today's events and formats agenda | 07:00 | `CALENDAR_CRON` |

> Default times can be adjusted via `GMAIL_CRON` / `CALENDAR_CRON`.
> For module-specific variables, refer to each module's documentation.

---

## First-Time Installation

### Step 1: Ask which modules to enable

Prompt the user:

```
The Morning Briefing skill includes the following modules. Which would you like to enable?

1. 📬 Gmail Daily Digest (default: 07:00)
2. 🗓 Google Calendar Agenda (default: 07:00)
3. Both

Please reply with a number.
```

Set the corresponding environment variables based on the user's response:
- Option 1 or 3: `ENABLE_GMAIL=true`
- Option 2 or 3: `ENABLE_CALENDAR=true`

### Step 2: Guide through gogcli setup

Inform the user:

```
Before proceeding, please complete the gogcli installation and authorization.
Follow the instructions in setup/gog-setup.md, then let me know when you're ready.
```

Indicate which services need to be authorized based on the enabled modules:
- Gmail only: `--services gmail`
- Calendar only: `--services calendar`
- Both: `--services gmail,calendar`

Wait for the user to confirm completion before proceeding.

### Step 3: Collect required variables

Ask the user for the following:

```
Please provide:
1. GOG_KEYRING_PASSWORD (your gogcli keyring password)
2. GOG_ACCOUNT (your Google account email)
3. GOG_TIMEZONE (e.g. Asia/Taipei, America/New_York)
```

### Step 4: Load the selected modules

Based on the enabled modules, load the corresponding files:
- `ENABLE_GMAIL=true` → load `modules/gmail-daily-digest.md`
- `ENABLE_CALENDAR=true` → load `modules/google-calendar-daily-brief.md`

### Step 5: Confirm setup complete

Send a confirmation message to Telegram:

```
🌅 Morning Briefing is ready!

Enabled modules:
📬 Gmail Daily Digest — every day at 07:00
🗓 Calendar Agenda — every day at 07:00

Let me know if you need any changes.
```

---

## Module Management

### Enable a module
```
enable gmail-daily-digest
enable google-calendar-daily-brief
```

### Disable a module
```
disable gmail-daily-digest
disable google-calendar-daily-brief
```

### Check current status
```
morning briefing status
which modules are enabled
```

---

## Global Variables

Shared across all modules:

| Variable | Description | Required |
|----------|-------------|----------|
| `GOG_KEYRING_PASSWORD` | gogcli keyring password | ✅ |
| `GOG_ACCOUNT` | Google account email | ✅ |
| `GOG_TIMEZONE` | User timezone (affects time display and cron calculation) | ✅ |
| `ENABLE_GMAIL` | Enable Gmail module | Default: `false` |
| `ENABLE_CALENDAR` | Enable Calendar module | Default: `false` |

> For module-specific variables (e.g. `GMAIL_CRON`, `MAX_RESULTS`), refer to each module's documentation.

---

## Related Files

- `setup/gog-setup.md` — gogcli installation and authorization guide
- `modules/gmail-daily-digest.md` — Gmail module documentation
- `modules/google-calendar-daily-brief.md` — Calendar module documentation
