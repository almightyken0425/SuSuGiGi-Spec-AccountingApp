# 完整備份邏輯: FullBackupLogic

## 目的

- L4 FullBackup 全量檔備份與還原規範
- 涵蓋 export 與 import 兩端，共用 JSON ZIP 結構與版本相容性規則
- JSON ZIP 內容為 Settings 加所有 entity 加 referential mapping
- 全 user 享有，與 analyticsConsent flag 完全脫鉤
- 用途為跨裝置遷移、誤刪保險、隱私退出
- 與 CSV import 區分：JSON ZIP 為全量本機間遷移，CSV 為單表帶外部資料進來

---

## exportFullBackup 匯出完整備份

- 序列化全量資料為 JSON ZIP，由使用者主動觸發

- **執行:**
  - 讀取本機 Settings 與下列 entity
    - transactions
    - transfers
    - accounts
    - categories
    - currency_rates
    - schedules
  - 收集 entity 之間的 referential mapping
  - 標記 schema version
  - 序列化為 JSON
  - 壓縮為 ZIP
  - 觸發 OS Share Intent，由使用者選擇去處
  - **IF** 操作失敗:
    - 不寫入暫存檔
    - **回傳:** 錯誤訊息

---

## importFullBackup 還原完整備份

- 從 JSON ZIP 還原全量資料至本機

- **執行:**
  - 由使用者選擇備份檔
  - 解壓 ZIP 並解析 JSON
  - 讀取 schema version
  - **IF** 版本不相容:
    - 終止還原
    - 提示版本不符
  - **ELSE:**
    - 引導使用者經過五步驟 Wizard
      - 顯示模板說明
      - 確認檔案來源
      - 顯示自動完成的欄位映射
      - 內容比對與差異列表
      - 預覽確認
    - **IF** 使用者確認:
      - 寫入本機 Settings 與所有 entity
      - 清除既有上次上傳時間
      - 後續由 TransactionBackupLogic 的 runInitialBackup 路徑自動同步至 Firestore
