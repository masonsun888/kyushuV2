# CLAUDE.md

九州 5 日旅遊手冊 — 兩份獨立的單檔 PWA 風格網頁。本檔給未來的 Claude Code session（含雲端 web session）快速接手用。

## 專案本質

- **純靜態網站**：沒有 build step、沒有套件、沒有 `package.json`、沒有測試或 linter。
- 每個頁面都是**單一 self-contained HTML**（CSS 與 JS 全部內嵌），可直接用瀏覽器開啟，部署只需丟到任何靜態主機。
- 語言介面為繁體中文（`lang="zh-TW"`），手機優先（iOS Safari「加到主畫面」的 PWA 體驗）。

## 檔案地圖

| 檔案 | 用途 | 主題色 | 標題 |
|------|------|--------|------|
| `index.html` | **個人版**（Mason 自己用） | 棕 `#b06b54` | 九州 5 日｜Mason |
| `group.html` | **團隊版**（員工旅遊） | 金 `#e8d49f` | Hi Y.P × THE TIMES 九州 5 日員工旅遊 |
| `logo.svg` / `logo.jpg` | 品牌 logo | | |

兩份頁面結構相似但內容、分頁、localStorage key 各自獨立 —— **改一邊不會自動影響另一邊**，需要時要兩邊都改。

## 頁面結構

- 頂部 `.sticky` 區：日圓→台幣換算機、預算進度條、下一個集合時間倒數（`#next-gather`）。
- `.tabs`（`#tabs`）橫向分頁，每個 `.tab` 帶 `data-tab="..."`，對應一個內容 section（`id` 同名）。
  - `index.html` 分頁：`day`（行程）、`buy`（採購/代買清單）、`tax`（退稅）、`coffee`、`book`、`tel`、`smoke`、`set`（設定）。
  - `group.html` 分頁：`day`、`morn`、`free`、`shop`、`buy`、`matcha`、`coffee`、`tax`、`spend`、`info`。
- 行程 section 內每個項目用 `id="dN-xxx"`（如 `d1-depart`、`d3-aso`）標記，方便定位單一行程點。

## 狀態與資料

全部存在瀏覽器 `localStorage`，無後端：

- `index.html`：`kyushu-spend`（記帳）、`kyushu-notes`、`kyushu-check`（勾選）、採購清單 key（程式內 `BUY_KEY`）。
- `group.html`：記帳 key（程式內 `STORE_KEY`）、`hiyp-check`、`hiyp-usage-collapsed`、採購清單 `BUY_KEY`。

## 版本與快取機制（重要）

- 每份頁面有 `const APP_VERSION='YYYY-MM-DD.n';`（如 `index.html` 的 `2026-05-17.1`）。
- HTML `<head>` 設了 `Cache-Control: no-cache`，並提供手動刷新鈕（`onclick="location.reload(true)"`，🔄）。
- **慣例：每次發布內容變更時手動 bump `APP_VERSION`**，方便確認使用者裝置拿到的是最新版（先前曾處理「加到主畫面不更新」的問題）。

## 在地化注意事項

- 時間混用台灣時間與日本時間（時差 1 小時）。歷史上修過「集合時間誤標時區」的 bug —— 改集合/行程時間時務必確認標的是哪個時區。

## 本地預覽（驗證改動）

沒有測試套件，驗證方式是**在瀏覽器實際操作**。起一個靜態伺服器：

```bash
python3 -m http.server 8000
# 開 http://localhost:8000/index.html 或 /group.html
```

改完 UI 後請實際點過分頁、換算機、勾選/記帳等互動再回報完成。

## 開發慣例

- 直接編輯對應的單一 HTML 檔；CSS/JS 都在檔案內，不要拆檔或引入框架（保持單檔可攜性）。
- 文案、emoji、繁中語氣與現有風格一致。
- 內容變更後記得 bump `APP_VERSION`。
- commit 訊息使用繁體中文，與現有 git log 風格一致。
