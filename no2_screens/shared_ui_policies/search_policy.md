# Search Policy 搜尋列共用元件政策

## 政策定位

- 本 module 內所有含搜尋輸入的 screen 共用政策
- 統一搜尋列位置、視覺、互動、token
- screen spec 只標模式代號，不重述輸入框細節
- 落地實作層遵照本政策共用元件實作

---

## 模式分類

- **模式 Bottom Pill — 底部固定 dock:**
    - **適用:**
        - 搜尋頁
        - 基礎幣別設定
        - 貨幣清單
        - 匯率紀錄清單
        - 時區設定
    - **元件條目順序:**
        - leading magnify icon
        - 中央 input
        - trailing clear button on focus
    - **互動:**
        - 永遠固定底部
        - 不隨 scroll 隱藏
        - 鍵盤彈出時 dock 緊貼鍵盤上緣
        - 鍵盤收起時 dock 落回底部
        - 自動補 safe area bottom inset
        - 點擊輸入框觸發 focus
        - 輸入內容時即時觸發 caller 篩選或搜尋
        - 含 clear button 一鍵清空

---

## 視覺 token

```yaml
SEARCH_BAR_TOKENS:
  PILL_HEIGHT: 44
  PADDING_HORIZONTAL: 16
  PADDING_VERTICAL: 12
  PILL_PADDING_HORIZONTAL: 12
  ICON_GAP: 8
  ICON_SIZE: 20
  INPUT_FONT_SIZE: 16

BOTTOM_SEARCH_BAR_TOTAL_HEIGHT: 68
```

---

## 互動規則

- **autoFocus 規則:**
    - 搜尋頁進入時 autoFocus
    - 設定型選擇頁不 autoFocus
    - autoFocus 時鍵盤直接彈出
    - dock 隨鍵盤上推

- **Keyboard 行為:**
    - iOS 用 `keyboardWillShow` 與 `keyboardWillHide`
    - Android 用 `keyboardDidShow` 與 `keyboardDidHide`
    - 動畫 duration 對齊系統事件值

- **Clear button:**
    - 採 React Native TextInput `clearButtonMode="while-editing"`
    - 僅 iOS 自動顯示
    - 點擊清空輸入並重新觸發 caller 篩選

- **Scroll 行為:**
    - 搜尋頁列表採 `keyboardShouldPersistTaps="handled"`
    - 搜尋頁列表 `onScrollBeginDrag` 觸發 `Keyboard.dismiss`

---

## 共用元件決策

- **新建元件:**
    - `BottomSearchBar`
        - 底部固定 pill dock
        - 自帶 keyboard 監聽與 safe area 處理
        - props 為 `value` / `onChangeText` / `placeholder` / `autoFocus`

- **保留:**
    - `GlassView`
        - 既有 pill 容器與 blur 效果

- **廢除:**
    - `FloatingSearchBar`
        - 頂部浮動 pill 加 scrollY 動畫元件
        - 與 navigation header 視覺競爭
        - caller 需 `Animated.Value` 與 `Animated.FlatList` 耦合
        - 改用 `BottomSearchBar` 取代

---

## screen spec 引用方式

- **要求:**
    - 各 screen spec 在搜尋列段落標註 `Search 模式: Bottom Pill`
    - 不重述 pill 結構
    - 不重述字級
    - 不重述 padding
    - 不重述視覺 token

- **合規寫法:**
```
### 搜尋輸入區

- Search 模式: Bottom Pill
- placeholder = i18n 鍵 common.search
- autoFocus 視 screen 需求
```

---

## 落地實作參考

- **caller 結構:**
    - 容器採 `<View>` 包列表元件與 `<BottomSearchBar>`
    - 列表採 `FlatList` 或 `ScrollView`
    - 不使用 `Animated.FlatList`
    - 不使用 `useRef(new Animated.Value)` 與 `Animated.event`

- **底部 padding:**
    - caller 需呼叫 `useSafeAreaInsets` 取 bottom inset
    - 列表 `contentContainerStyle.paddingBottom` 設為 `BOTTOM_SEARCH_BAR_TOTAL_HEIGHT + insets.bottom`
    - 確保最後一筆資料不被 dock 遮住

- **Safe area 處理:**
    - `BottomSearchBar` 內部呼叫 `useSafeAreaInsets`
    - 自動補 home indicator 邊距
    - caller 不需額外處理 dock 位置

- **與 ListGroupCard 整合:**
    - 含 `ListGroupCard` 包 long FlatList 場景
    - 仍採 `useHeaderInset` 控制 wrapper marginTop
    - dock 浮在 ListGroupCard 上層 z 順序
