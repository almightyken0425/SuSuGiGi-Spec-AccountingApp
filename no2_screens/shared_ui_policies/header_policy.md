# Header Policy 導航列共用元件政策

## 政策定位

- 本 module 內所有 screen 共用的導航列政策
- 統一所有 screen 的導航列模式、按鈕詞庫、視覺 token
- screen spec 只標模式代號，不重述按鈕細節
- 落地實作層遵照 React Navigation 原生 header 規範

---

## 模式分類

- **模式 A — Stack-List 列表頁:**
  - **適用:**
    - 設定主頁
    - 類別清單
    - 帳戶清單
    - 貨幣清單
    - 匯率清單
    - 偏好設定
    - 資料管理
    - 各類選擇器
  - **元件條目順序:**
    - 原生 chevron 返回
    - 標題
    - 動作元件
  - **動作元件條件:**
    - 通常為空
    - 列表類可放新增
    - 列表類可放合併
  - **背景:**
    - 透明
    - 內容滾動時依原生 Liquid Glass 自動 blur

- **模式 B — Stack-Editor 編輯頁:**
  - **適用:**
    - 預留
    - 目前無 screen 採用
  - **元件條目順序:**
    - 原生 chevron 返回
    - 動態標題
    - 動作元件留空
  - **背景:**
    - 同模式 A

- **模式 C — Modal-Form 表單型 modal:**
  - **適用:**
    - 交易編輯器
    - 轉帳編輯器
    - 帳戶編輯器
    - 類別編輯器
    - 合併編輯器
    - 匯率編輯器
    - 貨幣明細設定
    - 主題設定
    - 啟動模式設定
    - 主要貨幣設定
    - 語言設定
    - 時區設定
    - 匯入精靈
  - **元件條目順序:**
    - 關閉動作
    - 標題
    - 完成動作
  - **完成動作狀態:**
    - 表單必填欄位未滿足時設為 disabled
  - **背景:**
    - 同模式 A

- **模式 D — Modal-Info 純展示 modal:**
  - **適用:**
    - 首頁篩選
    - 付費牆
    - 搜尋頁
  - **元件條目順序:**
    - 關閉動作
    - 標題
    - 動作元件留空
  - **背景:**
    - 同模式 A
  - **搜尋頁特例:**
    - 搜尋輸入框視為 header 緊貼物
    - 搜尋輸入框 sticky 緊貼 header 下緣

- **模式 E — Special-Dashboard 主畫面儀表板:**
  - **適用:**
    - 首頁
  - **元件條目順序:**
    - 篩選動作
    - 標題或品牌名
    - 搜尋動作
    - 設定動作
  - **背景:**
    - 同模式 A

---

## 按鈕詞庫

- **絕對禁止:**
    - header 自訂按鈕含任何文字
    - 含中文文字
    - 含英文文字
    - 例外為標題本身
- **強制解法:**
    - 自訂按鈕一律 SF Symbol icon
    - icon 由本政策統一指定

| 動作 | 視覺                                   |
| ---- | -------------------------------------- |
| 返回 | 原生 chevron，不自訂                   |
| 關閉 | SF Symbol `xmark`                      |
| 完成 | SF Symbol `checkmark`                  |
| 新增 | SF Symbol `plus`                       |
| 合併 | SF Symbol `arrow.triangle.merge`       |
| 設定 | SF Symbol `gearshape`                  |
| 搜尋 | SF Symbol `magnifyingglass`            |
| 篩選 | SF Symbol `line.3.horizontal.decrease` |

---

## 視覺 token

```yaml
HEADER_TOKENS:
  background: transparent
  titleFontSize: 17
  titleFontWeight: 600
  titleColor: theme.text.primary
  iconSize: 17
  iconWeight: semibold
  iconColor: PlatformColor.label
  iconActiveColor: theme.primary.main
  iconDisabledColor: theme.text.disabled
  iconHitSlop: 8 8 8 8
  iconTouchablePadding: 10 10
```

---

## 內容區避開 header 規則

- **滾動容器規則:**
  - ScrollView 必加 contentInsetAdjustmentBehavior automatic
  - FlatList 必加 contentInsetAdjustmentBehavior automatic
  - SectionList 必加 contentInsetAdjustmentBehavior automatic
  - 禁止另設 paddingTop

- **純 View 容器規則:**
  - 透過 useHeaderInset hook 取得 header 高度
  - 對容器加 paddingTop 動態值

- **搜尋輸入框 sticky 規則:**
  - 搜尋輸入框 paddingTop 採用 useHeaderHeight 動態值
  - 搜尋輸入框緊貼 header 下緣
  - 搜尋輸入框不隨 FlatList 滾動

---

## 共用元件決策

- **保留:**
    - ModalCloseButton 已正確封裝 xmark icon
    - 採 padding 控制大小
    - 符合 iOS 26 Liquid Glass 圓圈

- **新建:**
    - HeaderCheckmarkButton 封裝 checkmark icon
    - HeaderCheckmarkButton 處理 disabled 狀態
    - HeaderIconButton 通用 SF Symbol icon 按鈕
    - useHeaderInset hook 包裝 useHeaderHeight

- **廢除:**
    - 手刻 View 形式的 ModalFormHeader
    - 手刻 View 形式的 ModalInfoHeader
    - 手刻 View 形式的 PushNavigationHeader

---

## screen spec 引用方式

- **要求:**
    - 各 screen spec 在 Header 佈局區域開頭標註 `Header 模式: A` 此類代號
    - 不重述按鈕詞庫
    - 不重述視覺 token
    - 不重述內容區避開規則

- **合規寫法:**
```
### Header

- Header 模式: C
- 關閉動作
- 標題
  - **IF** 支出:
    - 支出
  - **IF** 收入:
    - 收入
- 完成動作
  - **IF** 必填欄位未填妥:
    - 不可點按
```

---

## 落地實作參考

- **React Navigation 原生 header 技術指引:**
    - 已收錄於 ~/.claude/commands/rn-native-header.md
    - 含 createNativeStackNavigator 設定
    - 含 fullScreenModal 自訂 close 按鈕規範
    - 含 iOS 26 Liquid Glass patch 說明
