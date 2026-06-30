# App 啟動流程: AppBootstrapLogic

---

## bootstrapApp 啟動 App

- 確認 Firebase Auth 狀態，依登入結果調度背景任務並決定初始導航
- **執行:**
  - 讀取本地快取的 Firebase Auth 狀態
  - **IF** 已登入:
    - 呼叫 runCoreBackgroundTasks，於背景執行核心維護任務
    - 等待 PremiumLogic 的訂閱狀態就緒
    - 呼叫 resolveLaunchDestination，取得初始落點與付費牆攔截結果
    - 導航至初始落點
    - **IF** 攔截付費牆:
      - 導航至 PaywallScreen
  - **IF** 未登入:
    - 導航至 LoginScreen

---

## presentLaunchTransition 啟動轉場呈現

- 啟動期間先呈現 LaunchScreen 蓋住底層、避免空白底色，待條件滿足後依登入結果揭露 LoginScreen 或 HomeScreen
- **執行:**
  - **IF** 未登入:
    - 登入狀態確定後即揭露 LoginScreen
    - **理由:**
      - 未登入無使用者偏好可載，無須等待偏好
  - **IF** 已登入:
    - 待登入狀態確定且使用者偏好就緒後才揭露 HomeScreen
    - **理由:**
      - 僅等登入狀態確定即揭露，會先以預設偏好呈現再跳變為使用者偏好，故須再等偏好就緒
  - 揭露後移除 LaunchScreen 基礎態

---

## runCoreBackgroundTasks 執行核心背景任務

- 刷新 Premium 到期狀態，並補產生已到期的定期交易
- **性質:**
  - 非同步，不阻塞主流程
- **Premium 狀態更新:**
  - **執行:**
    - 向 IAP 服務查詢最新購買憑證，更新本地 Premium 到期狀態
- **定期交易補產生:**
  - **條件:**
    - 以當前時刻為截止點，比較排程實例時刻與當前時刻的絕對先後
    - 不依使用者時區的日界計算，以絕對時刻判定實例是否到期
  - **執行:**
    - 逐一檢視每筆 Schedule，自該排程最後一筆已產生實例的時刻往後推算；尚無任何實例時，自排程起始日起算
    - 依排程頻率與間隔，補產生所有實例時刻不晚於當前時刻、且未超過排程結束日的 Transactions 與 Transfers 紀錄
    - 同一排程同一實例時刻已存在紀錄時不重複產生，已刪除的實例亦不重新產生
- **交易備份自動觸發:**
  - **執行:**
    - 委派 TransactionBackupLogic 的 runBackup
  - **理由:**
    - 不重複冷卻時間判斷，閘控由 runBackup 自管

---

## handleForegroundResume 前景恢復觸發背景任務

- App 自背景恢復至前景且已登入時，重新觸發交易備份上傳
- 同一前景恢復事件另觸發 PremiumLogic 的 refreshStatus 重新刷新訂閱狀態，刷新行為由 PremiumLogic 規格定義
- **條件:**
  - 應用自背景回到前景
  - 使用者已登入
- **執行:**
  - 委派 TransactionBackupLogic 的 runBackup，上傳背景期間累積的本地變更
- **理由:**
  - 與啟動時共用同一備份委派入口，閘控由 runBackup 自管
  - 不補產生定期交易，定期補產生僅在登入後觸發

---

## resolveLaunchDestination 解析啟動導航目的地

- 依啟動模式與訂閱授權，回傳初始落點與是否攔截付費牆
- 於 PremiumLogic 訂閱狀態就緒後才解析，避免以首次更新前的佔位 LEVEL_0 誤判授權
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
    - **ELSE:**
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
    - **ELSE:**
      - **回傳:**
        - 初始落點 TransferEditorScreen
        - 不攔截付費牆
