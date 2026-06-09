# 定期交易核心邏輯: RecurringTransactions

## createSchedule 建立排程

- **輸入:**
  - 排程資料
  - 首筆交易資料
- **建立 Schedule:**
  - **執行:**
    - 於 `Schedules` 表建立一筆新記錄
- **產生首筆交易:**
  - **執行:**
    - **IF** TYPE 為 Transfer:
      - 呼叫 createTransfer，帶入 scheduleId 與 scheduleInstanceDate
    - **ELSE:**
      - 呼叫 createTransaction，帶入 scheduleId 與 scheduleInstanceDate

---

## updateSchedule 更新排程

- **輸入:**
  - 目標交易識別碼
  - 新交易資料
  - 更新選項
- **執行:**
  - **IF** 選項為僅此一筆:
    - 呼叫 updateTransaction 或 updateTransfer，不影響 `Schedules` 表
  - **IF** 此筆及未來:
    - 計算此筆交易日期的前一週期:
      - 依排程的 `frequency` 與 `interval`，從此筆交易日期往前推一個週期單位
      - 例: 排程為每月一次，此筆交易日期為 2026/3/15，前一週期為 2026/2/15
    - 將原 `Schedule` 的 `endOn` 設為前一週期日期
    - 刪除原 `Schedule` 自此筆交易日期當日起的所有實例
    - 依新內容建立新 `Schedule`，`startOn` 設為此筆交易日期，並自該日期依新規則補產生實例至當前時間

---

## deleteSchedule 刪除排程

- **輸入:**
  - 目標交易識別碼
  - 刪除選項
- **執行:**
  - **IF** 選項為僅此一筆:
    - 呼叫 deleteTransaction 或 deleteTransfer
  - **IF** 此筆及未來:
    - 計算此筆交易日期的前一週期: 同 updateSchedule
    - 將原 `Schedule` 的 `endOn` 設為前一週期日期
    - 刪除原 `Schedule` 自此筆交易日期當日起的所有實例

---

## generateMissingInstances 補產生實例

- 設計為於使用者登入或 App 啟動後呼叫，補齊離線期間到期而尚未產生的實例。createSchedule 僅補至建立當下，後續到期實例倚賴此操作補齊。
- 須具重入安全：重複呼叫或並行呼叫時，同一排程同一 instanceDate 至多產生一筆實例。
- **執行:**
  - 取得當前時間作為補產生截止點
  - 遍歷所有 `Schedules`，對每筆排程逐一處理，單筆排程失敗時跳過該筆、不中斷其餘排程：
    - 查詢此排程已產生的最後一筆實例：
      - **IF** 已有實例:
        - 取最後實例的 `scheduleInstanceDate` 為起點，依排程的 `frequency` 與 `interval` 推算下一個 instanceDate
      - **IF** 尚無實例:
        - 取 `startOn` 為起點 instanceDate
    - 從推算之 instanceDate 起迴圈：
      - **IF** instanceDate 晚於當前時間:
        - 結束此排程的迴圈
      - **IF** 排程設有 `endOn` 且 instanceDate 晚於 `endOn`:
        - 結束此排程的迴圈
      - **IF** 此排程已存在對應此 instanceDate 的實例:
        - 跳過，不重複產生此 instanceDate
      - 依排程類型呼叫 createTransfer 或 createTransaction，帶入 instanceDate 為 transactionDate、scheduleId 與 scheduleInstanceDate
      - 依 `frequency` 與 `interval` 推算下一個 instanceDate
