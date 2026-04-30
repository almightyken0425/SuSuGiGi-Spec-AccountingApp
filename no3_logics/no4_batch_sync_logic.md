# 批次同步邏輯: BatchSyncLogic

## 目的

- 登入使用者下，統一協調 L2 PreferenceSync 與 L3 TransactionBackup 兩條同步軌道
- L2 雙向同步委派至 PreferenceSyncLogic
- L3 單向上傳委派至 TransactionBackupLogic
- 控管 sync 起始時間 5 分鐘冷卻，防止反覆觸發燒 quota
- 冷卻時間戳裝置層級保留，跨 App 重啟與使用者切換不重置
- 不使用 Firestore Real-time listener，避免 read 成本

---

## sync 執行批次同步

- 雙向 L2 PreferenceSync 加上單向 L3 TransactionBackup

- **性質:**
  - 非同步執行
  - 需處理網路錯誤與 Firestore 配額錯誤
- **執行:**
  - **IF** 當前無登入使用者:
    - 終止流程
  - **IF** 同步已在進行中:
    - 終止流程
  - **IF** 距上次同步起始時間未滿 5 分鐘冷卻:
    - 終止流程
  - **ELSE:**
    - 標記同步進行中旗標
    - 記錄本次同步起始時間至本機持久化儲存
    - **L2 PreferenceSync 階段:**
      - **執行:**
        - 委派至 PreferenceSyncLogic 的 pullPreferenceFromCloud
        - push 由各 setXxx 函式即時觸發，本階段不重複處理
    - **L3 TransactionBackup 階段:**
      - **執行:**
        - 委派至 TransactionBackupLogic 的 checkBackupQuota
        - **IF** 配額允許:
          - **IF** Firestore 端無此 user 資料:
            - 委派至 TransactionBackupLogic 的 runInitialBackup
          - **ELSE:**
            - 委派至 TransactionBackupLogic 的 runDeltaBackup
        - **ELSE:**
          - 跳過本次 L3 上傳，等下次 UTC+0 跨日重置
    - 解除同步進行中旗標

---

## resetSyncState 重置同步狀態

- 清除上次上傳時間與同步進行中旗標
- 用於訂閱升級後強制全量重新上傳
