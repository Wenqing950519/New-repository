# gogcli Installation & Authorization Guide

This document covers the shared setup process for the `morning-briefing` skill suite.
All modules (Gmail, Calendar) depend on `gogcli`. Please complete the following steps in order.

---

## Step 1: Install gogcli

```bash
# macOS
brew install gogcli

# Linux / VPS (Ubuntu)
# Go is required to build from source
go version  # check if Go is installed

# If Go is not installed
sudo apt update && sudo apt install golang-go

# Build gogcli from source
git clone https://github.com/steipete/gogcli.git
cd gogcli && make
sudo mv gog /usr/local/bin/
```

Verify installation:
```bash
gog --version
```

---

## Step 2: Set environment variables

```bash
export GOG_KEYRING_PASSWORD='your_keyring_password'
export GOG_ACCOUNT='your_google_account@gmail.com'
export GOG_TIMEZONE='your_timezone'  # e.g. Asia/Taipei, America/New_York
```

To persist across sessions, add to `~/.bashrc` or `~/.zshrc`:

```bash
echo "export GOG_KEYRING_PASSWORD='your_keyring_password'" >> ~/.bashrc
echo "export GOG_ACCOUNT='your_google_account@gmail.com'" >> ~/.bashrc
echo "export GOG_TIMEZONE='your_timezone'" >> ~/.bashrc
source ~/.bashrc
```

> ⚠️ `your_keyring_password` can be any password you choose — it does not need to match your Google account password.

---

## Step 3: Create Google Cloud OAuth Credentials

> ⚠️ **This is where most people get stuck.** gogcli requires an OAuth client credentials file from Google Cloud Console before it can access any Google services.

### 3-1. Create a Google Cloud Project

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Click the project selector at the top → **New Project**
3. Enter a name (e.g. `openclaw-gog`) and click **Create**

### 3-2. Enable Required APIs

1. Go to **APIs & Services → Library**
2. Search for and enable:
   - `Gmail API`
   - `Google Calendar API`

### 3-3. Configure OAuth Consent Screen

1. Go to **APIs & Services → OAuth consent screen**
2. Select **External** user type (unless you have a Google Workspace org account), click **Create**
3. Fill in an app name (e.g. `openclaw`) and your email address
4. Skip the remaining fields and click **Save and Continue** through to the end
5. Back on the consent screen page, click **Publish App** — this prevents your tokens from expiring after 7 days

> If you see an "App not verified" warning during authorization, this is normal for personal projects. Click **Advanced → Go to (unsafe)** to proceed.

### 3-4. Create OAuth Client ID

1. Go to **APIs & Services → Credentials**
2. Click **+ Create Credentials → OAuth client ID**
3. Application type: **Desktop app**
4. Enter any name (e.g. `gogcli`), click **Create**
5. Click **Download JSON** and save as `client_secret_xxxx.json`

### 3-5. Load the Credentials

```bash
gog auth credentials ~/Downloads/client_secret_xxxx.json
```

No error output means it loaded successfully.

> **⚠️ VPS / headless servers (`--remote` mode):** If you're running this on a remote server without a browser, add `http://localhost:1` to the **Authorized redirect URIs** in your OAuth client settings in Cloud Console. Without this, the Step 1 authorization URL will fail to redirect correctly.

---

## Step 4: OAuth Authorization

Choose the authorization command based on the modules you want to enable:

### Gmail only
```bash
gog auth add $GOG_ACCOUNT --services gmail --remote --step 1
gog auth add $GOG_ACCOUNT --services gmail --remote --step 2
```

### Calendar only
```bash
gog auth add $GOG_ACCOUNT --services calendar --remote --step 1
gog auth add $GOG_ACCOUNT --services calendar --remote --step 2
```

### Both (recommended)
```bash
gog auth add $GOG_ACCOUNT --services gmail,calendar --remote --step 1
gog auth add $GOG_ACCOUNT --services gmail,calendar --remote --step 2
```

> Step 1 outputs a URL. Open it in your browser and complete the Google authorization flow,
> then run Step 2 and paste in the authorization code.

### Add a new service to an existing authorization
```bash
gog auth add $GOG_ACCOUNT --services gmail,calendar --force-consent
```

---

## Step 5: Verify Authorization

```bash
# Verify Gmail
gog gmail search 'is:unread' --max 1 --json

# Verify Calendar
gog calendar calendars --json
```

A successful JSON response from both commands confirms authorization is working.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `gog: command not found` | Confirm installation completed, or restart your terminal |
| Authorization failed / token expired | Re-run the Step 4 authorization commands |
| Keyring unlock failed | Confirm `GOG_KEYRING_PASSWORD` is correctly exported |
| Credentials file not found | Re-run Step 3 and verify the JSON file path |
| Linux build failed | Confirm Go version >= 1.21 by running `go version` |
| App not verified warning | Normal for personal projects — click **Advanced → Go to (unsafe)** |
| Token expires after 7 days | Make sure you published the OAuth consent app (Step 3-3) |
| Step 2 fails with "invalid code" | Paste only the `state` and `code` parameters — **do not paste the full redirect URL** |
| Re-authorizing one service breaks others | Always specify **all** services you need in a single `--services` flag — re-authorizing with a subset replaces the entire token |
