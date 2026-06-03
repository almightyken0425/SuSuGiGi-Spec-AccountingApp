# 交易編輯器: TransactionEditorScreen

## 畫面目標

- 提供新增與編輯收入或支出交易的介面，並作為建立定期收支排程的入口

---

## 線框圖

```text
+--------------------------------+
| [X]         Title         [✓]  |
+--------------------------------+
|  Date: Oct 25, 2024            |
+--------------------------------+
| [ $ Amount Input    ] [Recur]  |
|                                |
| [Category Selector]            |
| [Account Selector]             |
| [Note Input]                   |
|                                |
|           [Delete]             |
+--------------------------------+
```

---

## 佈局

### Header

- Header 模式: C
- 關閉按鈕
- 標題
  - **IF** 支出:
    - 支出
  - **IF** 收入:
    - 收入
- 完成按鈕
  - **IF** 必填欄位未填妥:
    - 不可點按

### 日期選擇區

- 日期選擇器模式: Calendar Dialog · Datetime

### 欄位區

- 類別選擇器
- 帳戶選擇器
- 備註輸入框

### 金額輸入區

- 金額輸入框
- 定期切換按鈕
  - **IF** 此筆交易屬於某排程:
    - 啟用視覺狀態
  - **ELSE:**
    - 停用視覺狀態

### 定期設定區

- **IF** 定期設定區為展開:
  - 嵌入 RecurringOptions

### 操作區

- **IF** 編輯模式:
  - 刪除 按鈕
    - 標籤依 delete_button_policy 規範

---

## 互動

- **進入畫面:**
  - **IF** 編輯模式:
    - 依此筆交易的當前內容顯示各欄位
    - **IF** 此筆交易屬於某排程:
      - 當前定期規則為該排程的頻率、間隔、結束日期
    - **IF** 此筆交易不屬於任何排程:
      - 當前定期規則為空
  - **IF** 新增模式:
    - 當前定期規則為空
  - 定期設定區預設為收合

- **點按關閉按鈕:**
  - 返回上一頁

- **點按定期切換按鈕:**
  - 呼叫 canUserPerformAction，動作識別碼 createRecurringTransaction
  - **IF** 回傳禁止:
    - 導航至 PaywallScreen
  - **ELSE:**
    - 切換定期設定區的展開或收合狀態

- **點按完成按鈕:**
  - **IF** 定期交易:
    - **IF** 新增模式:
      - 呼叫 createSchedule
    - **IF** 編輯模式:
      - 顯示定期編輯模式對話框
      - **IF** 選擇僅此一筆 或 此筆及未來:
        - 呼叫 updateSchedule
  - **IF** 一般交易:
    - **IF** 新增模式:
      - 呼叫 canUserPerformAction，動作識別碼 createTransaction
      - **IF** 回傳禁止:
        - 導航至 PaywallScreen
      - **ELSE:**
        - 呼叫 createTransaction
    - **IF** 編輯模式:
      - 呼叫 updateTransaction
  - **IF** 操作成功:
    - 返回上一頁
  - **IF** 操作失敗:
    - 顯示錯誤提示

- **點按刪除按鈕:**
  - **IF** 定期交易:
    - 顯示定期刪除模式對話框
    - **IF** 選擇僅此一筆 或 此筆及未來:
      - 呼叫 deleteSchedule
  - **IF** 一般交易:
    - 呼叫 deleteTransaction
  - **IF** 操作成功:
    - 返回上一頁
  - **IF** 操作失敗:
    - 顯示錯誤提示
