# 交易備份邏輯: TransactionBackupLogic

## 目的

- L3 TransactionBackup BackupEngine 規範
- 將 transactions、transfers、accounts、categories、currency_rates、schedules 寫入 Firestore users/{uid}/{collection}
- 全 user 寫入，含 LEVEL_0 免費版，不受 analyticsConsent flag 影響
- 法律基礎為 Contract，履行記帳服務契約所必要
- 單向上傳，無 user 端取回介面；user 取回走 L4 FullBackupLogic
- 不使用 Firestore Real-time listener
- 由 BatchSyncLogic 統一協調觸發
- 禁止從銀行 API 直接讀取交易資料
- 禁止透過 OS AI 自動爬取 mail、簡訊、支付 App 通知
- 不執行自動分類建議
- 不依賴外部即時匯率 API，採 Strict Direct Rate

---

## runInitialBackup 執行初次完整上傳

- 偵測 Firestore 端無此 user 資料時觸發，一次性將本地所有 entity 推送至 Firestore

- **執行:**
  - 偵測 Firestore 端是否存在此 user 的資料
  - **IF** Firestore 端無資料:
    - 讀取本地的 transactions、transfers、accounts、categories、currency_rates、schedules
    - 依集合分批寫入 Firestore，每批 500 筆
    - 累計上傳筆數至當日寫入配額
    - 寫入完成後切換為 Delta 模式
    - 更新上次上傳時間
  - **ELSE:**
    - 終止流程，後續走 runDeltaBackup

---

## runDeltaBackup 執行增量上傳

- 以本機修改時間戳篩選 Delta，避免重複傳輸

- **執行:**
  - 讀取本機自上次上傳後的變更，涵蓋 transactions、transfers、accounts、categories、currency_rates、schedules
  - 依集合分批寫入 Firestore users/{uid}/{collection}，每批 500 筆
  - 累計上傳筆數至當日寫入配額
  - 更新上次上傳時間

---

## checkBackupQuota 預檢配額

- 在執行 runInitialBackup 與 runDeltaBackup 前呼叫

- **執行:**
  - 委派至 FirestoreQuotaLogic 的 checkQuota
  - 讀取寫入允許旗標
  - **IF** 旗標為禁止:
    - 暫停 L3 上傳直到下次 UTC+0 跨日重置
    - **回傳:** false
  - **ELSE:**
    - **回傳:** true
