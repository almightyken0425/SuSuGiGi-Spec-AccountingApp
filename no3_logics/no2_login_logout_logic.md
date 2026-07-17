# 登入登出流程: LoginLogoutLogic

## handleLogin 執行登入流程

- **執行:**
  - 觸發 Google Sign-In 第三方認證程序，取得 ID Token
  - **IF** Google Sign-In 連線或認證失敗:
    - 顯示登入失敗提示
    - RETURN
  - **ELSE:**
    - 以 ID Token 向 Firebase Auth 進行身份驗證
    - **IF** Firebase Auth 驗證失敗:
      - 顯示登入失敗提示
      - RETURN
    - **ELSE:**
      - 呼叫 handlePostAuth

## logout 登出

- **執行:**
  - 保留本地帳務資料，不執行清除（清除僅在 handleReLogin 偵測到不同帳號時，依使用者選擇執行）
  - 觸發 Firebase Auth 登出，清除本地登入憑證
  - **IF** Firebase Auth 登出失敗:
    - 仍將本地登入狀態清為登出

---

## handleReLogin 處理重新登入

- **性質:**
  - 非同步，需處理網路錯誤
- **執行:**
  - **IF** 與本地快取帳號相同:
    - 委派至 PreferenceUploadLogic 的 uploadAllPreferences
    - 委派至 TransactionBackupLogic 的 runBackup
  - **ELSE:**
    - 通知使用者本地存有其他帳號資料，清除為不可復原操作，須明示選擇保留或清除、不自動清除
    - **IF** 使用者選擇清除:
      - 委派至 clearLocalData，清除前一帳號的本機資料
      - 清除前一帳號的本地授權快取，不殘留其付費等級於裝置；快取以帳號為範圍，只清前一帳號那份、不動當前帳號
    - **ELSE:**
      - 保留本機資料，直接繼續

---

## clearLocalData 清除本機資料

- 換帳號選擇清除時呼叫，硬重置整個本機資料庫；與資料管理的 resetAllData 軟刪刻意不同
- **性質:**
  - 非同步
- **執行:**
  - 清空本機資料庫全部紀錄，不分 userId
  - 採硬重置直接清空，不逐筆標記刪除，故不傳播雲端刪除標記；換帳號只清本機，不可動前一帳號的雲端資料
  - 清除後由 handlePostAuth 重建新帳號的 User 與 Settings