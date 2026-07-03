# 帳戶核心邏輯: AccountLogic

## createAccount 建立帳戶

- **輸入:**
  - 帳戶資料
- **IF** 帳戶名稱去除前後空白後長度超過名稱長度上限:
  - **回傳:** 驗證失敗，原因為名稱過長
  - 作為畫面即時長度上限的後備，涵蓋繞過畫面的匯入路徑
- **寫入 Account:**
  - **條件:**
    - `iconId` 必須引用 `tags` 含 `account` 的 `IconDefinitions` 記錄
  - **執行:**
    - 新增一筆記錄至 `Accounts` 表
- **IF** 幣別非主要貨幣:
  - 呼叫 createInitialCurrencyRate 種入佔位匯率

## updateAccount 更新帳戶

- **輸入:**
  - 帳戶資料
- **IF** 帳戶名稱去除前後空白後長度超過名稱長度上限:
  - **回傳:** 驗證失敗，原因為名稱過長
  - 作為畫面即時長度上限的後備，涵蓋繞過畫面的匯入路徑
- **更新 Account:**
  - **執行:**
    - 更新 `Accounts` 表中的記錄

## deleteAccount 刪除帳戶

- **輸入:**
  - 帳戶識別碼
- **軟刪除 Account:**
  - **執行:**
    - 更新 `Accounts` 表
- **串聯軟刪除 Transaction:**
  - **執行:**
    - 更新該帳戶所屬的 `Transactions` 表記錄
- **串聯軟刪除 Transfer:**
  - **執行:**
    - 更新該帳戶作為轉出方或轉入方的 `Transfers` 表記錄

## reorderAccounts 重排帳戶

- **輸入:**
  - 有序的帳戶識別碼清單
- **更新 Account:**
  - **執行:**
    - 批次更新 `Accounts` 表的排序欄位
