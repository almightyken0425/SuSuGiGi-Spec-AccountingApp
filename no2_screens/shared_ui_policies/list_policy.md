# List Policy 列表共用元件政策

## 政策定位

- 本 module 內所有 screen 共用的列表政策
- 統一所有 list 場景的視覺模式、互動樣式、token
- screen spec 只標模式代號，不重述 row 細節
- 落地實作層遵照本政策共用元件實作
- 高度客製化的特殊資料列表標 Custom，不套用本政策

---

## 模式分類

- **模式 A — Navigation 列表:**
    - **適用:**
        - 設定主頁
        - 偏好設定
        - 在地化設定
        - 資料管理
        - 貨幣清單
        - 匯率紀錄清單
    - **元件條目順序:**
        - leading icon
        - 主標
        - 副標可選
        - trailing 值文字可選
        - chevron
    - **互動:**
        - 點擊跳轉至下層 screen 或開啟 modal
        - 按下整列背景變淡灰

- **模式 B — Selection 列表:**
    - **B-1 單列適用:**
        - 語言設定
        - 啟動模式
        - 時區
        - 基礎幣別
        - 首頁篩選帳戶
        - Currency Rate Editor 內幣別選擇 modal
        - Account Editor 內幣別與類型 picker
        - Category Editor 內標準分類 picker
        - Currency Detail Config 小數位數
    - **B-2 網格適用:**
        - 主題設定
    - **B-1 元件條目順序:**
        - leading icon 可選
        - 主標
        - 副標可選
        - trailing checkmark
    - **B-2 元件條目順序:**
        - 預覽 children slot
        - 名稱
        - checkmark overlay
    - **互動:**
        - 單選或多選
        - 選中以 checkmark 標示
        - 按下整列背景變淡灰
        - 多選用法不變更 trailing 樣式
        - HomeFilter 採綠色 checkbox 為產品既有 UX 例外

- **模式 C — 簡化資料列表:**
    - **適用:**
        - 直接顯示一條條資料且可點擊跳編輯頁
        - row 結構接近 A 模式
    - **說明:**
        - 視為 A 模式變體，使用相同 ListItem
        - 含金額換算、多幣別、分組折疊、搜尋 highlight 等複雜資料列表標為 Custom 不套用

- **模式 D — Reorderable 列表:**
    - **適用:**
        - 帳戶清單
        - 類別清單
    - **元件條目順序:**
        - leading icon
        - 主標
        - 副標可選
    - **互動:**
        - 長按拖拉排序
        - 點擊整列導向編輯
        - 不啟用按下變淡灰，避免拖拉手勢衝突

- **Custom Layout 例外:**
    - **適用:**
        - 高度客製化資料列表
        - 例如首頁交易 pager
        - 例如 Period 交易列表含多幣別匯率換算與分組折疊
        - 例如搜尋結果含關鍵字 highlight
        - 例如匯入精靈
    - **規範:**
        - screen spec 標 `List 模式: Custom`
        - 不要求對齊本政策視覺 token
        - 仍應使用 LIST_TOKENS 中的字級、字重、padding 對齊

---

## 視覺 token

```yaml
LIST_TOKENS:
  ITEM_MIN_HEIGHT: 58
  ITEM_PADDING_VERTICAL: 17
  ITEM_PADDING_HORIZONTAL: 16
  ITEM_GAP_HORIZONTAL: 12
  ITEM_TITLE_SIZE: 17
  ITEM_TITLE_WEIGHT: '300'
  ICON_SIZE_SMALL: 20
  ICON_SIZE_MEDIUM: 24
  ICON_SIZE_LARGE: 40
  DIVIDER_COLOR_LIGHT: 'rgba(60,60,67,0.10)'
  DIVIDER_INSET_WITH_ICON: 48
  DIVIDER_INSET_WITHOUT_ICON: 16
  GROUP_CARD_RADIUS: 14
  GROUP_CARD_MARGIN_BOTTOM: 35
  SECTION_TITLE_SIZE: 13
  SECTION_TITLE_WEIGHT: '400'
  SECTION_TITLE_LETTER_SPACING: 0.5
  SECTION_TITLE_PADDING_TOP: 12
  SECTION_TITLE_PADDING_BOTTOM: 6
  SECTION_TITLE_PADDING_HORIZONTAL: 16
  SELECTION_ITEM_RADIUS: 8
  SELECTION_CHECKMARK_SIZE: 16
  TRAILING_CHEVRON_SIZE: 13
  TRAILING_CHEVRON_WEIGHT: 'semibold'
  TRAILING_VALUE_SIZE: 17
  GRID_COLUMNS: 2
  GRID_GAP: 12
  EMPTY_STATE_ICON_SIZE: 48
  EMPTY_STATE_TITLE_SIZE: 17
  EMPTY_STATE_DESCRIPTION_SIZE: 14
  EMPTY_STATE_ICON_GAP: 12
  EMPTY_STATE_TEXT_GAP: 8
  EMPTY_STATE_PADDING_HORIZONTAL: 24
```

---

## 互動規則

- **按下回饋:**
    - A / B 模式採 Pressable
    - 按下整列背景變 `theme.bg.surface_hover`
    - D 模式不變色，避免拖拉手勢衝突

- **Disabled 樣式:**
    - 主標顏色變 `theme.text.disabled`
    - 整列 opacity 0.5 由 caller 視需求加
    - 點擊回饋停用

- **Selected 樣式:**
    - B-1 單列 trailing 顯示 `FontAwesome check` icon
    - B-1 selected 色為 `theme.primary.main`
    - B-2 網格右上 `FontAwesome check-circle` overlay 標示選中

- **空狀態:**
    - 任何含搜尋的列表必加 `ListEmptyState`
    - 搜尋無結果採 title 為 `common.no_results`
    - 搜尋無結果採 description 為 `「{searchQuery}」`
    - 初始無資料採該 screen 對應的描述性訊息
    - 載入中不顯示空狀態

- **空狀態切換:**
    - 含 `ListGroupCard` 的搜尋列表必用 `ListEmptyTransition` 包覆兩態
    - empty 態不渲染卡片底色避免視覺殘留
    - 兩態以 crossfade 動畫互換避免硬切
    - `topInset` 對應 `useHeaderInset` 結果
    - `bottomInset` 對應 `BOTTOM_SEARCH_BAR_TOTAL_HEIGHT + insets.bottom`
    - 動畫期間舊態 pointerEvents 設為 none 避免誤觸

---

## 共用元件決策

- **新建元件:**
    - `ListItem`
        - A / B 共用基底
        - 承載 `leftIcon` / `title` / `subtitle` / `trailing` / `value` / `showChevron` / `titleColor` / `disabled` 八個 slot
    - `ListGroupCard`
        - 圓角群組卡片包覆器
        - 自動以 overflow hidden 圓角 clip 第一個 row 的 borderTop
    - `ListSection`
        - section title 與 body 容器
        - title 套 LIST_TOKENS 規範
    - `ListSeparator`
        - hairline 分隔線元件
    - `SelectionListItem`
        - B-1 單列選擇加 checkmark
    - `SelectionGridItem`
        - B-2 2 欄網格加 checkmark overlay 與 preview slot
    - `DataListItem`
        - 預留資料列元件
        - 簡化 C 模式可選用
    - `ReorderableListItem`
        - D 拖拉卡片
        - 拖拉手勢由整列觸發
    - `ListEmptyState`
        - 列表空狀態顯示元件
        - 提供 icon 與 title 與 description 三個 slot
        - 預設 icon 為 magnify
        - 適用搜尋無結果與初始無資料兩種情境
    - `ListEmptyTransition`
        - list 與 empty 兩態 crossfade 動畫包覆器
        - 內部以 absolute 疊兩層 Animated.View 對 opacity 互補補間
        - props 為 `isEmpty` 與 `emptyState` 與 `children` 與 `topInset` 與 `bottomInset` 與 `duration`
        - 預設 duration 220ms
        - 切換時自動處理 pointerEvents 避免互動衝突

- **保留:**
    - `FloatingSearchBar`
        - 既有 B / C 模式搜尋輸入元件
    - `DynamicIcon`
        - 既有 leading icon 渲染元件

- **廢除:**
    - 各 screen 內手刻的 `optionRow` / `rowButton` / `pickerItem` / `currencyItem` 樣式

---

## screen spec 引用方式

- **要求:**
    - 各 screen spec 在 list 區段開頭標註 `List 模式: A` 此類代號
    - 不重述 row 結構
    - 不重述字級
    - 不重述 padding
    - 不重述視覺 token

- **合規寫法:**
```
### List

- List 模式: A
- 列項
  - leading icon = 設定圖
  - 主標 = i18n 鍵 settings.manage_categories
  - chevron 跳轉至 CategoryList
```

- **組合寫法:**
    - 含多種模式的 screen 用 `+` 連接，例如 `List 模式: A+B-1+B-1`
    - 純 Custom Layout 標 `List 模式: Custom` 並補充說明 row 結構

---

## 落地實作參考

- **長 list:**
    - 條件
        - 資料量 > 30
    - 採用
        - `FlatList` 直接放在 `ListGroupCard` 內
        - row 元件自帶 hairline borderTop
    - 不再使用
        - `ItemSeparatorComponent`，避免與 row borderTop 重複

- **短 list:**
    - 條件
        - 資料量 < 10 或設定型分組
    - 採用
        - `ScrollView` + `ListGroupCard` + `ListItem` 或 `SelectionListItem`
        - GroupCard 圓角自動 clip 第一個 row borderTop

- **B-1 選擇 list:**
    - 採用
        - `FlatList` 或 `ScrollView` 內含 `SelectionListItem`
        - SelectionListItem 自帶 hairline borderTop

- **B-2 網格:**
    - 採用
        - `flexDirection: row` + `flexWrap: wrap`
        - 每格寬度 47% 加 `GRID_GAP`
        - 每格用 `SelectionGridItem` 包裝
        - children slot 放預覽內容

- **D 拖拉 list:**
    - 採用
        - `react-native-drag-sort` 的 `AutoDragSortableView`
        - 子元件 `ReorderableListItem` width 與 height 由 caller 透過 style 傳入
        - caller 傳的 height 必須與 `childrenHeight` 一致
        - ScrollView 容器使用 `useHeaderInset` 控制 paddingTop
        - ScrollView 不使用 `contentInsetAdjustmentBehavior`，避免 bouncing 區與拖拉手勢衝突

- **滾動容器規則:**
    - 與 header_policy 一致
    - FlatList / SectionList / ScrollView 必加 `contentInsetAdjustmentBehavior="automatic"`
    - D 模式為例外
    - 含 ListGroupCard 包 long FlatList 場景為例外

- **與 FloatingSearchBar 整合:**
    - 與 header_policy 一致
    - 需設 `paddingTop: FLOATING_SEARCH_BAR_TOTAL_HEIGHT` 與 `onScroll={onScroll}`
    - 包 ListGroupCard 的 long list 採 `useHeaderInset` 控制 wrapper marginTop
