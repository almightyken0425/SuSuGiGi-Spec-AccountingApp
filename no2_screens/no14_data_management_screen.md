# 資料管理: DataManagementScreen

## 畫面目標

- 提供資料匯入、匯出與清除的集中管理介面

---

## 佈局

### 導覽列

- Header 模式: A
- 返回按鈕
- 資料管理 標題

### 設定列表

- List 模式: A
- 各分組以空白間距區隔
- 不顯示分組標題文字
- 第一組
  - 匯入收入支出 入口
  - 匯入轉帳 入口
- 第二組
  - 匯出收入支出 入口
  - 匯出轉帳 入口
- 第三組
  - 清除資料庫 入口
    - 標題以紅色顯示
    - icon 以紅色顯示

---

## 互動

- **點按返回按鈕:**
  - 返回上一頁

- **點按匯入收入支出:**
  - 呼叫 canUserPerformAction 查核 importData 權限
  - **IF** 回傳禁止:
    - 導航至 PaywallScreen
  - **ELSE:**
    - 導航至 ImportWizardScreen，帶入交易模式

- **點按匯入轉帳:**
  - 呼叫 canUserPerformAction 查核 importData 權限
  - **IF** 回傳禁止:
    - 導航至 PaywallScreen
  - **ELSE:**
    - 導航至 ImportWizardScreen，帶入轉帳模式

- **點按匯出收入支出:**
  - 呼叫 canUserPerformAction 查核 exportData 權限
  - **IF** 回傳禁止:
    - 導航至 PaywallScreen
  - **ELSE:**
    - 呼叫 exportToCsv，匯出類型 交易
    - **IF** 操作中:
      - 阻擋重複觸發直到完成
    - **IF** 無資料可匯出:
      - 顯示無資料對話框
    - **IF** 操作失敗:
      - 顯示匯出失敗對話框

- **點按匯出轉帳:**
  - 呼叫 canUserPerformAction 查核 exportData 權限
  - **IF** 回傳禁止:
    - 導航至 PaywallScreen
  - **ELSE:**
    - 呼叫 exportToCsv，匯出類型 轉帳
    - **IF** 操作中:
      - 阻擋重複觸發直到完成
    - **IF** 無資料可匯出:
      - 顯示無資料對話框
    - **IF** 操作失敗:
      - 顯示匯出失敗對話框

- **點按清除資料庫:**
  - 顯示確認對話框
  - **IF** 確認清除:
    - 批次標記刪除所有資料表紀錄
    - **IF** 操作成功:
      - 顯示資料庫已清除並提示重啟 App 對話框
    - **IF** 操作失敗:
      - 顯示清除失敗對話框
