# 登入登出流程: LoginLogoutLogic

## handleLogin 執行登入流程

- **執行:**
  - 觸發 Google Sign-In 第三方認證程序，取得 ID Token
  - **IF** Google Sign-In 連線或認證失敗:
    - 顯示連線異常提示
    - RETURN
  - **ELSE:**
    - 以 ID Token 向 Firebase Auth 進行身份驗證
    - **IF** Firebase Auth 驗證失敗:
      - 顯示連線異常提示
      - RETURN
    - **ELSE:**
      - 呼叫 handlePostAuth

## logout 登出

- **執行:**
  - 清除本地 Premium 快取狀態
  - 保留本地帳務資料，不執行清除（清除僅在 handleReLogin 偵測到不同帳號時，依使用者選擇執行）
  - 觸發 Firebase Auth 登出，清除本地登入憑證
  - **IF** Firebase Auth 登出失敗:
    - 強制清除本地 Firebase session token，下次啟動時以 auth 狀態重新驗證

---

## handleReLogin 處理重新登入

- **性質:**
  - 非同步，需處理網路錯誤
- **執行:**
  - **IF** 與本地快取帳號相同:
    - 委派至 PreferenceUploadLogic 的 uploadAllPreferences
    - 委派至 TransactionBackupLogic 的 runBackup
  - **ELSE:**
    - 通知使用者本地存有其他帳號資料，依其選擇保留或清除後繼續
    - 清除為不可復原操作：本機清空後無法復原，雲端備份不會自動還原，故須使用者明示選擇，不自動清除