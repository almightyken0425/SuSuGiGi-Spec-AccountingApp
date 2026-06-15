# 語系設定: LanguageSettingScreen

## 畫面目標

- 提供選擇 App 介面語系的介面

---

## 線框圖

```text
+--------------------------------+
| [X]        Language        [✓] |
+--------------------------------+
| 繁體中文                    ✓  |
| Bahasa Indonesia               |
| Deutsch                        |
| English                        |
| ...                            |
+--------------------------------+
| [ Search...                  ] |
+--------------------------------+
```

---

## 佈局

### 導覽列

- Header 模式: C
- 關閉按鈕
- 語系 標題
- 完成按鈕

### 搜尋列

- Search 模式: Bottom Pill

### 語系列表

- List 模式: B-1
- 進入畫面時目前選取語系置頂，其餘照原生名稱字母序；其後選取其他語系不重新排序，順序維持至離開畫面
- 語系項目以原生名稱呈現，不附中文譯名
- 語系選項
  - 以原生名稱呈現，例如 English、繁體中文、日本語、한국어、Español
  - **IF** 為目前選取語系:
    - 顯示選取標記

### 支援語系清單

- 20 個語系，依 BCP 47 代碼識別
  - en — English
  - zh-Hant — 繁體中文
  - zh-Hans — 简体中文
  - ja — 日本語
  - ko — 한국어
  - de — Deutsch
  - es — Español
  - fr — Français
  - pt-BR — Português
  - it — Italiano
  - nl — Nederlands
  - sv — Svenska
  - da — Dansk
  - no — Norsk
  - fi — Suomi
  - pl — Polski
  - id — Bahasa Indonesia
  - vi — Tiếng Việt
  - th — ไทย
  - hi — हिन्दी

---

## 互動

- **點按關閉按鈕:**
  - 返回上一頁

- **輸入搜尋文字:**
  - 依語系代碼或原生名稱即時篩選列表

- **點按語系選項:**
  - 將此選項標為目前選取語系

- **點按完成按鈕:**
  - 呼叫 setLanguage
  - 返回上一頁

- **無搜尋結果:**
  - 顯示空狀態：找不到結果
