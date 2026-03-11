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

## Step 3：載入 OAuth Client Credentials

在進行帳號授權前，必須先儲存從 Google Cloud Console 下載的 OAuth client credentials：

```bash
gog auth credentials ~/Downloads/client_secret_xxxx.json
```

> ⚠️ 尚未建立 credentials 的用戶，請至 [Google Cloud Console](https://console.cloud.google.com/)
> → API 和服務 → 憑證 → 建立 OAuth 2.0 用戶端 ID，下載 JSON 檔案後再執行上述指令。

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
| credentials 找不到 | 重新執行 Step 3，確認 JSON 檔案路徑正確 |
| Linux 編譯失敗 | 確認 Go 版本 >= 1.21，執行 `go version` 確認 |
