# CLAUDE.md

本 repo 為 SuSuGiGi 產品的 **Module Spec git**，module_id 為 `no2_accounting_app`，承載記帳 App 的行為規格。

## 多層 git 配對

- **頂層 Product git：**
    - 位於 `../../`
    - 管理決策框架的上游各層
- **對側 Module Design git：**
    - 位於 Product git 的 `no4_product_designs/no2_accounting_app/`
    - React HTML design canvas（intro / foundations / components / screens / explorations 五個 tab）
- **本 Module Spec git：**
    - 即本 repo
    - 位於 Product git 的 `no3_product_specs/no2_accounting_app/`
- **對側 Module Impl git：**
    - 位於 Product git 的 `no5_product_development/no2_accounting_app/`
    - React Native CLI 記帳 App

完整路徑與配對表由 `decision_framework_router` skill 的 `products_registry.md` 維護。

配對 commit 使用相同 subject 與 body，branch 名稱逐字一致。

---

## 撰寫規範

所有規格文件依循以下分層政策。

- Model 層位於 `no1_data_models/`，套用 `spec_writer` 的 model 政策
- View 層位於 `no2_screens/`，套用 `spec_writer` 的 screen 政策
- Logic 層位於 `no3_logics/`，套用 `spec_writer` 的 logic 政策
- 跨層邊界另套用 `spec_writer/cross_layer_boundary_policy.md`
- 所有 .md 額外套用 `universal_writing_linter` 通用政策

修改前先 consult `decision_framework_router` skill 的上游 review 四問。

---

## 對側 git 對應路徑

Spec 變動時須檢查對應的 Design 視覺、Impl 實作與上游 Product Map。本表列出本 module 各檔案類型的對側承載點，作為搬遷與配對檢查依據。

### Design git 對側

- 視覺 token 具體值權威：`no4_product_designs/no2_accounting_app/project/10_foundations/data.jsx`
    - 已 export 群組：`TYPOGRAPHY`、`LIST_TOKENS`、`TX_LIST_TOKENS`、`SEARCH_BAR_TOKENS`、`SPACING`、`RADIUS`、`ICON_SIZE`、`HIT_TARGET`、`PALETTE`、`THEMES`、`GLASS`、`CHART_COLORS`、`ICON_LIBRARY`
- 視覺定案展示：`project/10_foundations/foundations.jsx` 的 DCSection
- 元件視覺與互動 sandbox：`project/20_components/`
- 畫面對應：`no2_screens/noN_<name>_screen.md` 對應 Design git 內 `project/30_screens/screens.jsx` 的 ScreenComponent

### Impl git 對側

- 資料模型對應：`no1_data_models/<X>.md` 對應 Impl 的 `src/database/models/<X>.ts` 與 migration
- 畫面對應：`no2_screens/noN_<name>_screen.md` 對應 Impl 的 `src/screens/<Name>/`
- 邏輯對應：`no3_logics/<X>.md` 對應 Impl 的 `src/contexts/`、`src/services/` 或 `src/hooks/`
- coding guideline 承載點：Impl repo 的 `CLAUDE.md` 之「開發注意事項」章節（含元件名、自定 hook、平台 API、第三方套件、廢除清單）

### 上游 Product git

- Product Map 對應子目錄：`../../no2_product_planning/no2_product_map/no2_accounting_app/`
- 需求層：`../../no2_product_planning/no1_requirements/`，牽涉新問題時補條目

---

## 共用 UI 政策

`no2_screens/shared_ui_policies/` 承載跨 screen 共用的 UI 規範。screen spec 標模式代號引用，不重述細節。

- `header_policy.md`：導航列模式 A 列表頁 / B 編輯頁 / C 表單 modal / D 純展示 modal / E 主畫面儀表板 / F 多步驟精靈
- `list_policy.md`：列表模式 A 導航 / B 選擇（B-1 單列、B-2 網格）/ C 簡化資料 / D 拖拉 / Custom 例外
- `search_policy.md`：搜尋模式 Bottom Pill 底部固定 dock
- `date_picker_policy.md`：日期選擇器模式 Datetime 日期加時間 / Date-only 純日期
- `delete_button_policy.md`：editor 類 screen 共用刪除按鈕，統一標籤與觸發行為
- `undo_bar_policy.md`：全域底部復原列，screen 觸發處標 `呼叫 showUndo`，狀態機由 no3_logics 的 UndoLogic 承載

---

## 術語規範

本 module 特有詞彙延續頂層 Product git 定義。

- 付費等級使用 `LEVEL_0`、`LEVEL_1`、`LEVEL_2`、`LEVEL_3`、`LEVEL_B` 形式
- LEVEL 的商業定義由頂層 Product git 的 `no1_product_initiation/no3_business_model.md` 承載
- LEVEL 在本 module 視角下的能力清單由 Product Map 的 `no2_accounting_app/` 子目錄或既有 `app/payment.md` 承載
