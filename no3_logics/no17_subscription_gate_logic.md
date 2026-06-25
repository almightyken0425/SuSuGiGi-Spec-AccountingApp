# 訂閱授權邏輯規格: SubscriptionGateLogic

## 目的

- 定義各訂閱等級可執行的受限建立動作條件與總數限制
- 規則由 canUserPerformAction 取用，作為判斷唯一依據

---

## 參照來源

- 跨 Module 的 LEVEL 商業定義: `no1_product_initiation/no3_business_model.md`
- 記帳 App 視角下各 LEVEL 可用能力的白話總覽: `no2_product_planning/no2_product_map/app/payment.md` 的 LEVEL 能力清單
- 本檔的規則表與上述兩者對齊，任一變更時需同步更新

---

## LEVEL 規則表

- **LEVEL_0:**
  - 允許動作，受總數限制:
    - createAccount
    - createCategory
    - createTransaction
    - createTransfer
  - 禁止動作: 無
    - createTransaction / createTransfer 帳戶或類別總數超過上限時連帶被擋
    - 帳戶或類別總數等於上限時仍允許新增交易與轉帳
    - 總數僅在匯入後可能超過上限，匯入不經本動作的配額判斷
  - 總數限制:
    - 帳戶總數上限 3 個
    - 類別總數上限 7 個
- **LEVEL_1 以上:**
  - 允許動作: 全部
  - 禁止動作: 無
  - 總數限制: 無
  - 說明:
    - LEVEL_2 記帳 App 視角下能力與 LEVEL_1 相同
    - 授權判斷採用當前訂閱等級大於等於 LEVEL_1 即允許的形式
    - LEVEL_1 相對 LEVEL_0 的唯一差異是解除帳戶 / 類別 / 交易的總數上限

---

## canUserPerformAction 判斷使用者是否可執行動作

- **輸入:**
  - 當前使用者訂閱等級
  - 動作識別碼，合法值為以下其一:
    - createAccount
    - createCategory
    - createTransaction
    - createTransfer
- **性質:**
  - 純本地計算，可讀取當前使用者的帳戶總數與類別總數
  - 帳戶與類別總數計入該使用者未軟刪除的所有列
  - 已停用的列仍計入，停用不退還配額
  - 僅軟刪除的列排除於計數
  - 選擇器另排除停用列，但不影響配額計數
- **執行:**
  - **IF** 動作識別碼為 createAccount:
    - **IF** 當前訂閱等級為 LEVEL_0:
      - 讀取當前使用者的帳戶總數
      - **IF** 帳戶總數小於 3:
        - **回傳:** 允許
      - **ELSE:**
        - **回傳:** 禁止
    - **ELSE:**
      - **回傳:** 允許
  - **IF** 動作識別碼為 createCategory:
    - **IF** 當前訂閱等級為 LEVEL_0:
      - 讀取當前使用者的類別總數
      - **IF** 類別總數小於 7:
        - **回傳:** 允許
      - **ELSE:**
        - **回傳:** 禁止
    - **ELSE:**
      - **回傳:** 允許
  - **ELSE:**
    - **IF** 當前訂閱等級為 LEVEL_0:
      - 讀取當前使用者的帳戶總數與類別總數
      - **IF** 帳戶總數超過 3 或類別總數超過 7:
        - **回傳:** 禁止
      - **ELSE:**
        - **回傳:** 允許
    - **ELSE:**
      - **回傳:** 允許
