# App 啟動流程: AppBootstrapLogic

---

## bootstrapApp 啟動 App

- 確認 Firebase Auth 狀態，依登入結果調度背景任務並決定初始導航
- **執行:**
  - 讀取本地快取的 Firebase Auth 狀態
  - **IF** 已登入:
    - 呼叫 runCoreBackgroundTasks，於背景執行核心維護任務
    - 呼叫 resolveLaunchDestination，取得初始落點與付費牆攔截結果
    - 導航至初始落點
    - **IF** 攔截付費牆:
      - 導航至 PaywallScreen

---

## runCoreBackgroundTasks 執行核心背景任務

- 刷新 Premium 到期狀態，並依日期條件補產生定期交易
- **性質:**
  - 非同步，不阻塞主流程
- **Premium 狀態更新:**
  - **執行:**
    - 向 IAP 服務查詢最新購買憑證，更新本地 Premium 到期狀態
- **定期交易補產生:**
  - **條件:**
    - 以使用者時區計算的當前日期晚於上次定期交易檢查日
  - **執行:**
    - 依 Schedules 表的排程設定，補產生所有尚未建立的 Transactions 與 Transfers 紀錄
    - 更新上次定期交易檢查日為當前日期
- **交易備份自動觸發:**
  - **執行:**
    - 委派 TransactionBackupLogic 的 runBackup
  - **理由:**
    - 不重複冷卻時間判斷，閘控由 runBackup 自管

---

## resolveLaunchDestination 解析啟動導航目的地

- 依啟動模式與訂閱授權，回傳初始落點與是否攔截付費牆
- 啟動模式指向 editor 時，先經 canUserPerformAction 閘控
- 訂閱受限不變動啟動模式，僅以付費牆攔截當次啟動
- **執行:**
  - 讀取 Settings 表的 launchMode
  - **IF** launchMode 為 home:
    - **回傳:**
      - 初始落點 HomeScreen
      - 不攔截付費牆
  - **IF** launchMode 為 expense 或 income:
    - 呼叫 canUserPerformAction，動作識別碼 createTransaction
    - **IF** 回傳禁止:
      - **回傳:**
        - 初始落點 HomeScreen
        - 攔截付費牆
    - **IF** 回傳允許:
      - **IF** launchMode 為 expense:
        - **回傳:**
          - 初始落點 TransactionEditorScreen 支出模式
          - 不攔截付費牆
      - **IF** launchMode 為 income:
        - **回傳:**
          - 初始落點 TransactionEditorScreen 收入模式
          - 不攔截付費牆
  - **IF** launchMode 為 transfer:
    - 呼叫 canUserPerformAction，動作識別碼 createTransfer
    - **IF** 回傳禁止:
      - **回傳:**
        - 初始落點 HomeScreen
        - 攔截付費牆
    - **IF** 回傳允許:
      - **回傳:**
        - 初始落點 TransferEditorScreen
        - 不攔截付費牆
