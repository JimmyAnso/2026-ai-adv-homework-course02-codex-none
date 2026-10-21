# 花卉電商網站（backend-project）

花卉電商全端應用，提供前台商品瀏覽、購物車、訂單管理，以及後台商品與訂單管理功能。支援訪客購物車（X-Session-Id）與會員登入（JWT）雙模式。

## 技術棧

| 分類 | 技術 | 版本 |
|------|------|------|
| 執行環境 | Node.js | — |
| Web 框架 | Express | ~4.16.1 |
| 資料庫 | SQLite（better-sqlite3） | ^12.8.0 |
| 模板引擎 | EJS | ^5.0.1 |
| CSS 框架 | Tailwind CSS | ^4.2.2 |
| 認證 | JSON Web Token（jsonwebtoken） | ^9.0.2 |
| 密碼雜湊 | bcrypt | ^6.0.0 |
| ID 生成 | uuid（v4） | ^11.1.0 |
| CORS | cors | ^2.8.5 |
| 環境變數 | dotenv | ^16.4.7 |
| API 文件 | swagger-jsdoc | ^6.2.8 |
| 測試框架 | Vitest | ^2.1.9 |
| HTTP 測試 | supertest | ^7.2.2 |
| 瀏覽器 E2E | Chrome DevTools MCP | ^1.11.0 |

## 快速開始

```bash
# 1. 安裝依賴
npm install

# 2. 設定環境變數
cp .env.example .env
# 編輯 .env，至少設定 JWT_SECRET

# 3. 啟動伺服器（含 CSS 編譯）
npm run start
# 伺服器預設在 http://localhost:3001

# 4. 開發模式（分別啟動）
npm run dev:server   # 啟動 Express 伺服器
npm run dev:css      # Tailwind CSS watch 模式

# 5. 執行測試
npm run test

# 6. 生成 OpenAPI 文件
npm run openapi
```

### 預設帳號

| 角色 | Email | 密碼 |
|------|-------|------|
| 管理員 | admin@hexschool.com | 12345678 |

管理員帳號在伺服器首次啟動時自動建立（可透過 `ADMIN_EMAIL` / `ADMIN_PASSWORD` 環境變數自訂）。

## 瀏覽器 E2E 測試

本專案以 **Chrome DevTools MCP** 驅動 Chrome，透過實際前臺操作驗證登入、加入購物車、結帳，以及綠界 staging 金流回跳。這與 `npm run test` 執行的 Vitest／Supertest API 測試不同；此流程不使用 Playwright，也不直接修改訂單或付款資料。

### 使用測試 Skill

在 Codex 專案對話中輸入 `$storefront-e2e`，即可呼叫本專案的 E2E 測試 Skill。也可以附上額外範圍，例如：

```text
$storefront-e2e 請測試登入、商品加入購物車與綠界 staging 付款，使用有頭 Chrome，完成後附上截圖。
```

Skill 位於 [`skills/storefront-e2e/SKILL.md`](../skills/storefront-e2e/SKILL.md)，會檢查 `localhost:3001` 服務、沿著商店 UI 完成流程、使用綠界測試環境的 WebATM 模擬付款，並在結束時保存截圖。若有可延續的既有測試訂單，會先檢查狀態以避免重複建單；成功與否以回到商店後的訂單狀態為準。測試結束時只會關閉本次測試自行啟動的 3001 伺服器；若原本已有服務在執行，會保持不動。

### 工具與設定

- `chrome-devtools-mcp` 是專案的 npm 開發依賴，設定檔為 [`.vscode/mcp.json`](../.vscode/mcp.json)。MCP 以有頭模式啟動 Chrome，並允許將截圖寫入專案工作區。
- 測試網站使用 `http://localhost:3001`，請勿改用 `127.0.0.1`，以免回跳後瀏覽器 Cookie 網域不同。
- 若在 Codex 中沒有可用的 Chrome DevTools MCP 連線，可在專案根目錄啟動 MCP server：

  ```bash
  npm exec -- chrome-devtools-mcp --no-headless --workspace "$PWD"
  ```

  保持 MCP server 執行，再由支援 MCP 的用戶端連線並使用瀏覽器工具。`--no-headless` 是 MCP server 的啟動參數。
- 若 3001 port 沒有服務，Skill 會以 `npm run dev:server` 啟動開發伺服器；測試完（包含失敗）會關閉自己啟動的伺服器並確認 port 已釋放。若服務原本已在執行，Skill 不會關閉它。也可以手動啟動後再呼叫 Skill；此時伺服器不屬於 Skill 啟動的程序，不會由 Skill 關閉。

### 測試注意事項與截圖

- 只使用綠界 staging/mock WebATM 流程，不輸入真實銀行資料、不進行實際轉帳。ECPay 中選擇「網路 ATM」及「台灣土地銀行」後，只能透過測試頁提供的模擬結果完成流程。
- 使用假收件資料；不要把密碼、真實個資放進截圖或測試回報。
- 每次測試（包含失敗）都會將有代表性的 MCP 截圖保存到 `test-results/`，並在回覆中附上連結。此目錄已加入 `.gitignore`，截圖不會進入 Git 提交。
- 若綠界回報訂單編號重複、服務不可用，或商店沒有顯示「已付款」，應保留截圖並回報最後成功的檢查點，不要反覆重試或宣稱付款成功。

## 常用指令

| 指令 | 說明 |
|------|------|
| `npm run start` | 編譯 CSS + 啟動伺服器 |
| `npm run dev:server` | 僅啟動伺服器 |
| `npm run dev:css` | Tailwind CSS watch 模式 |
| `npm run css:build` | 編譯並壓縮 CSS |
| `npm run test` | 執行全部測試 |
| `npm run openapi` | 生成 openapi.json |

## 文件索引

| 文件 | 說明 |
|------|------|
| [ARCHITECTURE.md](./ARCHITECTURE.md) | 架構、目錄結構、資料流 |
| [DEVELOPMENT.md](./DEVELOPMENT.md) | 開發規範、命名規則、計畫歸檔流程 |
| [FEATURES.md](./FEATURES.md) | 功能列表與完成狀態（含行為描述） |
| [TESTING.md](./TESTING.md) | 測試規範與指南 |
| [CHANGELOG.md](./CHANGELOG.md) | 更新日誌 |
| [plans/](./plans/) | 開發計畫目錄 |
| [plans/archive/](./plans/archive/) | 已完成計畫歸檔 |
