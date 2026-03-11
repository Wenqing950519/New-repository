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

## Step 3: Load OAuth Client Credentials

Before authorizing, you must first load the OAuth client credentials downloaded from Google Cloud Console:

```bash
gog auth credentials ~/Downloads/client_secret_xxxx.json
```

> ⚠️ If you haven't created credentials yet, go to [Google Cloud Console](https://console.cloud.google.com/)
> → APIs & Services → Credentials → Create OAuth 2.0 Client ID, then download the JSON file and run the command above.

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
