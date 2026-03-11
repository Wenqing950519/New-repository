# gogcli 安裝與授權指南

本文件為 `morning-briefing` skill 套件的共用安裝流程。
所有模組（Gmail、Calendar）皆依賴 `gogcli` 工具，請依序完成以下步驟。

---

## Step 1：安裝 gogcli

```bash
# macOS
brew install gogcli

# Linux / VPS（Ubuntu）
# 需先安裝 Go 環境
go version  # 確認是否已安裝

# 若未安裝 Go
sudo apt update && sudo apt install golang-go

# 從源碼編譯 gogcli
git clone https://github.com/steipete/gogcli.git
cd gogcli && make
sudo mv gog /usr/local/bin/
```

安裝完成後確認：
```bash
gog --version
```

---

## Step 2：設定環境變數

```bash
export GOG_KEYRING_PASSWORD='your_keyring_password'
export GOG_ACCOUNT='your_google_account@gmail.com'
export GOG_TIMEZONE='your_timezone'  # 例如 Asia/Taipei、America/New_York
```

建議加入 `~/.bashrc` 或 `~/.zshrc` 永久生效：

```bash
echo "export GOG_KEYRING_PASSWORD='your_keyring_password'" >> ~/.bashrc
echo "export GOG_ACCOUNT='your_google_account@gmail.com'" >> ~/.bashrc
echo "export GOG_TIMEZONE='your_timezone'" >> ~/.bashrc
source ~/.bashrc
```

> ⚠️ `your_keyring_password` 請自行設定，不需與 Google 帳號密碼相同。

---

## Step 3：建立 Google Cloud OAuth 憑證

> ⚠️ **這一步是最多人卡關的地方。** gogcli 需要你在 Google Cloud Console 建立一組 OAuth client credentials，才能代表你存取 Google 服務。

### 3-1. 建立 Google Cloud 專案

1. 前往 [Google Cloud Console](https://console.cloud.google.com/)
2. 點擊頂部的專案選擇器 → **新增專案**
3. 名稱輸入例如 `openclaw-gog`，點擊**建立**

### 3-2. 啟用所需 API

1. 前往 **API 和服務 → 程式庫**
2. 依序搜尋並啟用：
   - `Gmail API`
   - `Google Calendar API`

### 3-3. 設定 OAuth 同意畫面

1. 前往 **API 和服務 → OAuth 同意畫面**
2. 使用者類型選 **外部**（除非你有 Google Workspace 組織帳號），點擊**建立**
3. 填入應用程式名稱（隨意，例如 `openclaw`），填入你的 Email
4. 其餘欄位跳過，一路點**儲存並繼續**直到完成
5. 回到同意畫面，點擊**發布應用程式**（否則 token 只有 7 天有效期）

> 如果你看到「應用程式尚未驗證」的警告，這是正常的個人專案現象，點擊**進階 → 前往（不安全）**繼續授權即可。

### 3-4. 建立 OAuth 用戶端 ID

1. 前往 **API 和服務 → 憑證**
2. 點擊 **+ 建立憑證 → OAuth 用戶端 ID**
3. 應用程式類型選 **桌面應用程式**
4. 名稱隨意（例如 `gogcli`），點擊**建立**
5. 點擊 **下載 JSON**，儲存為 `client_secret_xxxx.json`

### 3-5. 載入 Credentials

```bash
gog auth credentials ~/Downloads/client_secret_xxxx.json
```

執行後沒有報錯即完成。

> **⚠️ 重要：`--remote` 模式的 Redirect URI 設定**
> 如果你是在無介面的 VPS 上操作（使用 `--remote` 參數），需要在 Cloud Console 的 OAuth 用戶端設定裡，將 `http://localhost:1` 加入**已授權的重新導向 URI**，否則 Step 1 的授權連結會無法正確回呼。

---

## Step 4：OAuth 授權

依照你要啟用的模組，選擇對應的授權指令：

### 僅啟用 Gmail
```bash
gog auth add $GOG_ACCOUNT --services gmail --remote --step 1
gog auth add $GOG_ACCOUNT --services gmail --remote --step 2
```

### 僅啟用 Calendar
```bash
gog auth add $GOG_ACCOUNT --services calendar --remote --step 1
gog auth add $GOG_ACCOUNT --services calendar --remote --step 2
```

### 兩個都啟用（建議）
```bash
gog auth add $GOG_ACCOUNT --services gmail,calendar --remote --step 1
gog auth add $GOG_ACCOUNT --services gmail,calendar --remote --step 2
```

> Step 1 會輸出一組 URL，請在瀏覽器開啟並完成 Google 授權後，
> 再執行 Step 2 並貼入授權碼。

### 已有授權，補加新服務
```bash
gog auth add $GOG_ACCOUNT --services gmail,calendar --force-consent
```

---

## Step 5：驗證授權

```bash
# 驗證 Gmail
gog gmail search 'is:unread' --max 1 --json

# 驗證 Calendar
gog calendar calendars --json
```

兩個指令都有回傳 JSON 資料即代表授權成功。

---

## 常見問題

| 問題 | 解決方式 |
|------|----------|
| `gog: command not found` | 確認安裝步驟完成，或重新開啟終端機 |
| 授權失敗 / token 過期 | 重新執行 Step 4 的授權指令 |
| keyring 解鎖失敗 | 確認 `GOG_KEYRING_PASSWORD` 環境變數已正確 export |
| credentials 找不到 | 重新執行 Step 3-5，確認 JSON 檔案路徑正確 |
| Linux 編譯失敗 | 確認 Go 版本 >= 1.21，執行 `go version` 確認 |
| 出現「應用程式未驗證」警告 | 個人專案正常現象，點擊**進階 → 前往（不安全）**繼續 |
| Token 7 天後失效 | 確認已在 Step 3-3 發布 OAuth 同意畫面的應用程式 |
| Step 2 貼入授權碼失敗 | 只貼 `state` 和 `code` 參數，**不要貼完整的 redirect URL** |
| 重新授權某個服務後其他服務失效 | 重新授權時必須在 `--services` 一次列出**所有**需要的服務，否則會覆蓋掉整個 token |
