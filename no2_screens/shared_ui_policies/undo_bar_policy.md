# Undo Bar Policy 復原列共用元件政策

## 政策定位

- Undo Bar 為全域底部覆蓋元件，不屬任一 screen
- 符合條件的可復原操作完成後，覆蓋於使用者返回的當前畫面底部
- screen spec 不重述 Undo Bar 結構，只在觸發處標註 `呼叫 showUndo`
- 狀態機行為由 no3_logics 的 UndoLogic 承載
- 視覺定案由對側 Design git 的 `10_foundations/` 與 `20_components/` 承載
- 實作元件、平台 API、套件選用由對側 Impl git 的 `CLAUDE.md` UI Coding Guideline 承載

---

## 出現時機

- 由 showUndo 觸發顯示
- 觸發來源:
    - 交易編輯器刪除交易（一般或定期，定期含僅此一筆與此筆及未來）
    - 轉帳編輯器刪除轉帳（一般或定期）
    - 帳戶編輯器刪除帳戶
    - 類別編輯器刪除類別
    - 合併編輯器完成合併
- 上述操作多發生於編輯器畫面，編輯器關閉後 Undo Bar 覆蓋於返回的目的畫面（首頁或對應列表）
- 覆蓋顯示後維持約 4 秒，期間無互動則自動關閉；倒數行為由 UndoLogic 承載
- 全域單一實例，後一次操作取代前一次的復原等待

---

## 元件條目順序

- 復原段：倒數計時與復原訊息文字並列，整段為單一觸發區
- 取消段：取消動作

---

## 互動

- **點按復原段:**
    - 呼叫 executeUndo
- **點按取消段:**
    - 呼叫 closeUndo
- **倒數歸零:**
    - 呼叫 closeUndo

---

## screen spec 引用方式

- **要求:**
    - 編輯器 screen 在刪除或合併成功處標註 `呼叫 showUndo`
    - 不重述 Undo Bar 結構與互動
    - 不宣告 Undo Bar 為某 screen 的局部元件
- **合規寫法:**

```
- **IF** 操作成功:
  - 呼叫 showUndo
  - 返回上一頁
```
