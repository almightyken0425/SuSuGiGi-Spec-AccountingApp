# 分類核心邏輯: CategoryLogic

## createCategory 建立分類

- **輸入:**
  - 分類資料（含 type、name、standardCategoryId、iconId、disabledOn）
- **欄位約束:**
  - `type` 為必填，須為 `expense` 或 `income`
  - `standardCategoryId` 必須屬於與 `type` 同邊的標準分類
  - `iconId` 必須引用 `tags` 含 `category` 的 `IconDefinitions` 記錄
- **寫入 Category:**
  - **執行:**
    - 新增一筆記錄至 `Categories` 表

## updateCategory 更新分類

- **輸入:**
  - 分類識別碼、可更新欄位
- **可更新欄位:**
  - `name`、`standardCategoryId`、`iconId`、`disabledOn`
- **禁止更新欄位:**
  - `type`：避免改變已建立分類的類型，導致歷史交易分類錯亂
- **欄位約束:**
  - 更新後的 `standardCategoryId` 須仍屬於該分類 `type` 同邊的標準分類
- **更新 Category:**
  - **執行:**
    - 更新 `Categories` 表中的記錄

## deleteCategory 刪除分類

- **輸入:**
  - 分類識別碼
- **軟刪除 Category:**
  - **執行:**
    - 更新 `Categories` 表
- **串聯軟刪除 Transaction:**
  - **執行:**
    - 更新該分類所屬的 `Transactions` 表記錄

## reorderCategories 重排分類

- **輸入:**
  - 分類類型
  - 有序的分類識別碼清單
- **更新 Category:**
  - **執行:**
    - 批次更新 `Categories` 表的排序欄位
