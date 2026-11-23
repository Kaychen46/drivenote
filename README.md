# DriveNote — GitHub Pages 部署與 Google Drive 同步指南

本部署包包含完整的 **靜態登入殼＋離線 PWA＋Google Drive 雙向同步** 檔案：

- `index.html`：主程式（含登入保護、五大核心模組、所有擴充功能、Google Drive API v3 同步與每日自動快照）
- `manifest.json`：PWA 設定檔（支援手機加入主畫面全螢幕開啟）
- `sw.js`：離線快取 Service Worker（無網路時也能秒開網頁並暫存於瀏覽器）

---

## 步驟一：上傳至 GitHub 並啟用 GitHub Pages

1. 在 GitHub 建立一個新的 Repository（例如 `drivenote`）。
2. 將本資料夾內的 `index.html`、`manifest.json`、`sw.js` 推送到該 Repository 的 `main` 分支根目錄。
3. 進入 Repository 的 **Settings → Pages**：
   - **Source** 選擇 `Deploy from a branch`
   - **Branch** 選擇 `main` / `/ (root)` 並點擊 **Save**。
4. 等待 1–2 分鐘，記下你的專屬網址（例如 `https://<你的帳號>.github.io/drivenote/`）。

---

## 步驟二：設定 Google Cloud OAuth 2.0（僅限本人登入白名單）

1. 前往 [Google Cloud Console](https://console.cloud.google.com/) 建立一個免費專案（例如 `DriveNote-Personal`）。
2. 在左側選單前往 **APIs & Services → Library**，搜尋並啟用 **Google Drive API**。
3. 前往 **Google Auth Platform → Branding / Audience**（或 OAuth consent screen）：
   - User Type 選擇 **External（外部）**。
   - 發布狀態維持在 **Testing（測試中）**（切勿公開發布，如此陌生人無法登入）。
   - 在 **Test users（測試使用者）** 清單中，**只加入你自己的 Gmail**。
   - 在 **Data Access / Scopes** 加入非敏感權限：`https://www.googleapis.com/auth/drive.file`。
4. 前往 **Clients（憑證）→ Create Client**：
   - Application type 選擇 **Web application**。
   - 在 **Authorized JavaScript origins（已授權的 JavaScript 來源）** 加入你的 GitHub Pages 網域（**只填到網域，不要加結尾斜線與子路徑**，例如 `https://<你的帳號>.github.io`）。
   - 建立完成後，複製你的 **Client ID**（格式為 `xxxx.apps.googleusercontent.com`）。

---

## 步驟三：填入 Client ID 並開始跨裝置同步

- **方式 A（免改程式碼）**：打開你的 GitHub Pages 網址，點擊左下角「設定 Google OAuth Client ID」貼上並儲存，再點「使用 Google 帳號登入並同步 Drive」。
- **方式 B（固定寫入）**：打開 `index.html`，將 `const DEFAULT_GOOGLE_CLIENT_ID = '';` 填入你的 Client ID 後重新推送，以後換任何新電腦或手機打開網址都不必再貼一次。

首次登入同步後，系統會自動在你的 Google Drive 建立：
- `MyPersonalNotebook/notebook_data.json`（主資料庫）
- `MyPersonalNotebook/backups/YYYY-MM-DD.json`（每日首次同步自動快照）
- `MyPersonalNotebook/attachments/`（日記上傳的圖片與檔案附件）

---

## 步驟四：手機加入主畫面（PWA）與日後換主機

- **iPhone (Safari)**：開啟網址 → 點底部「分享」→「加入主畫面」。
- **Android (Chrome)**：開啟網址 → 右上角選單 →「安裝應用程式」或「加到主畫面」。
- **日後換其他主機**（如 Vercel、Render Static Site、Cloudflare Pages）：只要把這三個檔案搬過去，並在同一個 Google Cloud OAuth Client ID 新增新的「已授權的 JavaScript 來源」網址，即可無縫接續同一份 Google Drive 資料。
