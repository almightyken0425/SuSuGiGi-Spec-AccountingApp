# 交易核心邏輯: TransactionLogic

## normalizeTransactionAmount 交易金額正規化

- 人類輸入金額換算為 `amount` 整數儲存值
- **輸入:**
  - 原始輸入金額
  - 交易型別，expense 或 income
  - 幣別的 `useThousandsUnit` 設定
- **性質:**
  - 純本地計算
- **執行:**
  - **IF** `useThousandsUnit` 啟用:
    - 原始輸入金額先乘以一千
  - 金額乘以一萬縮放為整數
  - **IF** 交易型別為 expense:
    - 取絕對值後轉為負值
  - **ELSE:**
    - 取絕對值，維持正值
- **回傳:**
  - 整數儲存金額

---

## createTransaction 建立交易

- **輸入:**
  - 交易資料
  - `amount` 為 normalizeTransactionAmount 的輸出
- **IF** 輸入缺少必填欄位 `userId`、`accountId`、`categoryId`、`amount`、`date` 其中之一:
  - **回傳:** 驗證失敗
- **IF** amount 等於 0:
  - **回傳:** 驗證失敗
- **IF** amount 超出可儲存範圍:
  - **回傳:** 驗證失敗，原因為金額超出範圍
- **ELSE:**
  - **寫入 Transaction:**
    - **執行:**
      - 新增一筆記錄至 `Transactions` 表

---

## updateTransaction 更新交易

- **輸入:**
  - 交易資料
  - `amount` 為 normalizeTransactionAmount 的輸出
- **IF** 輸入缺少必填欄位 `userId`、`accountId`、`categoryId`、`amount`、`date` 其中之一:
  - **回傳:** 驗證失敗
- **IF** amount 等於 0:
  - **回傳:** 驗證失敗
- **IF** amount 超出可儲存範圍:
  - **回傳:** 驗證失敗，原因為金額超出範圍
- **ELSE:**
  - **更新 Transaction:**
    - **執行:**
      - 更新 `Transactions` 表中的記錄
  - **排程連結保留:**
    - **行為:**
      - `scheduleId` 與 `scheduleInstanceDate` 逐欄位獨立判斷
      - 輸入有帶該欄位才改寫
      - 未帶則保留記錄原值

---

## deleteTransaction 刪除交易

- **輸入:**
  - 交易識別碼
- **軟刪除 Transaction:**
  - **執行:**
    - 更新 `Transactions` 表
