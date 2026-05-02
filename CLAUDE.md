# CLAUDE.md

本 repo 為 SuSuGiGi 產品的 **Module Spec git**，module_id 為 `no2_accounting_app`，承載記帳 App 的行為規格。

## 三層 git 配對

- **頂層 Product git：**
    - 位於 `../../`
    - 管理決策框架的上游四層
- **本 Module Spec git：**
    - 即本 repo
    - 位於 Product git 的 `no3_product_specs/no2_accounting_app/`
- **對側 Module Impl git：**
    - 位於 Product git 的 `no5_product_development/no2_accounting_app/`
    - React Native CLI 記帳 App

完整路徑與配對表由 `decision_framework_router` skill 的 `products_registry.md` 維護。

---

## 撰寫規範

所有規格文件依循以下分層政策。

- Model 層位於 `no1_data_models/`，套用 `spec_writer` 的 model 政策
- View 層位於 `no2_screens/`，套用 `spec_writer` 的 screen 政策
- Logic 層位於 `no3_logics/`，套用 `spec_writer` 的 logic 政策
- 所有 .md 額外套用 `universal_writing_linter` 通用政策

修改前先 consult `decision_framework_router` skill 的上游 review 四問。

---

## 共用 UI 政策

`no2_screens/shared_ui_policies/` 承載跨 screen 共用的 UI 規範。screen spec 標模式代號引用，不重述細節。

- `header_policy.md`
    - 導航列模式
    - 模式 A 列表頁
    - 模式 B 編輯頁
    - 模式 C 表單 modal
    - 模式 D 純展示 modal
    - 模式 E 主畫面儀表板
- `list_policy.md`
    - 列表共用元件政策
    - 模式 A 導航列表
    - 模式 B 選擇列表，分 B-1 單列與 B-2 網格
    - 模式 C 簡化資料列表
    - 模式 D 拖拉列表
    - Custom 例外
- `search_policy.md`
    - 搜尋列共用元件政策
    - 模式 Bottom Pill 底部固定 dock

---

## 術語規範

本 module 特有詞彙延續頂層 Product git 定義。

- 付費等級使用 `LEVEL_0`、`LEVEL_1`、`LEVEL_2`、`LEVEL_3`、`LEVEL_B` 形式
- LEVEL 的商業定義由頂層 Product git 的 `no1_product_initiation/no3_business_model.md` 承載
- LEVEL 在本 module 視角下的能力清單由 Product Map 的 `no2_accounting_app/` 子目錄或既有 `app/payment.md` 承載

---

## 配對變動規則

Spec 變動時通常需要三層聯動檢查。

- 對側 Impl 需檢查對應實作
    - `no1_data_models/<X>.md` 對應 Impl 的 `src/database/models/<X>.ts` 與 migration
    - `no2_screens/noN_<name>_screen.md` 對應 Impl 的 `src/screens/<Name>/`
    - `no3_logics/<X>.md` 對應 Impl 的 `src/contexts/`、`src/services/` 或 `src/hooks/`
- 上游 Product Map 需確認本變動已反映
- 需求層若牽涉新問題需補 `no1_requirements/` 條目

配對 commit 使用相同 subject 與 body，branch 名稱逐字一致。
