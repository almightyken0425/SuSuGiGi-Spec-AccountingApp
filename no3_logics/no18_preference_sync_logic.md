# 偏好同步邏輯: PreferenceSyncLogic

## 目的

- L2 PreferenceSync 的同步政策層
- 定義 push 與 pull 邊界、同步範圍、衝突解決原則
- push 入口委派至 SettingsManagement 的 updateUserPreferences
- pull 入口委派至 PostAuthLogic 的 syncSettingsFromCloud
- 排程觸發由 BatchSyncLogic 統一協調
- 不使用 Firestore Real-time listener，避免 read 成本
- 不涉及交易、轉帳、帳戶、類別、匯率、定期交易；那些屬 L3 TransactionBackupLogic

---

## getSyncedPreferenceFields 取得同步欄位清單

- 回傳所有參與 L2 同步的 preference 欄位識別碼

- **回傳:**
  - 欄位識別碼清單
    - baseCurrencyId
    - theme
    - language
    - timeZone
    - launchMode

---

## pushPreferenceToCloud 上傳偏好至雲端

- L2 PreferenceSync 的上行同步入口

- **輸入:**
  - 欲變更的偏好欄位與目標值
- **執行:**
  - 委派至 updateUserPreferences
  - 全 user 享有，不做 Premium 篩選
  - 以 dot notation 逐欄更新 preferences
  - 自動更新 preferences.updatedAt 為當下時間
  - **IF** 寫入失敗:
    - 僅記 log，不阻塞 UI

---

## pullPreferenceFromCloud 從雲端拉取偏好

- L2 PreferenceSync 的下行同步入口
- 由 BatchSyncLogic 排程或登入流程呼叫

- **執行:**
  - 委派至 syncSettingsFromCloud
  - 採 Last-Write-Wins，比較 preferences.updatedAt 與 Settings.updatedOn
  - 涵蓋 getSyncedPreferenceFields 列舉的全部欄位
