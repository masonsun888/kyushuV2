# 九州 5 日旅遊手冊

行動裝置優先的旅遊行程手冊網頁 App。單一 HTML 檔、純前端、部署於 GitHub Pages，
加到手機主畫面即可離線使用。

## 網址
- **團隊版**（同行夥伴用）：https://masonsun888.github.io/kyushuV2/group.html
- **個人版**（私人計畫用）：https://masonsun888.github.io/kyushuV2/

## 檔案
| 檔案 | 說明 |
|---|---|
| `group.html` | 團隊版（Hi Y.P store 品牌，奶油黃配色，無個人資料）|
| `index.html` | 個人版（紅褐配色，含個人採購/預算/規劃）|
| `logo.jpg` / `logo.svg` | 品牌 logo（真實圖檔 + SVG fallback）|
| `.claude/skills/travel-planning/` | 「旅遊規劃」Skill，可複製到未來旅遊專案 |

## 功能模組
- 📅 行程：5 天時間軸，當日自動展開，集合時間醒目標示
- 🛏️ 起床：每天建議起床／早餐時間
- 🆓 自由：自由時段景點玩法
- 🛍️ 購物：各商場品牌目錄
- 🍵 抹茶：採購完整指南（店家/品牌/日文點購語）
- ☕ 咖啡：每天精選 1-2 家精品咖啡
- 🧾 退稅：每店 checklist + 樓層/櫃台位置
- 💴 記帳：新增/編輯/刪除，現金/刷卡分開，每筆顯示台幣
- 🛒 我的代買：圖片上傳 + 數量 + 代買對象 + 台幣換算
- ℹ️ 資訊：緊急聯絡、飯店、日本資訊、匯率設定、版權

## 技術架構
- 單一 HTML（內嵌 CSS + 原生 JS，無框架、無後端）
- 資料存 `localStorage`（純本地，不上傳）
- GitHub Pages 部署（push main 自動更新）
- PWA-friendly（加到主畫面離線可用）

## 更新流程
1. 改 HTML → bump `APP_VERSION`
2. 合併到 `main` 分支（`git merge --ff-only`）
3. GitHub Pages 1-2 分鐘自動部署
4. 使用者按頂部 🔄 按鈕或下拉刷新拿新版

## 重點技術筆記（踩雷備忘）
- **時區**：用 `Date.UTC` 手動計算，不靠瀏覽器解析 `+09:00` 字串（iOS Safari 會出包）
- **今日判斷**：用本地日期（`localDateStr`），不用 `toISOString()`（UTC 會差一天）
- **匯率**：開 app 抓即時（open.er-api.com，免金鑰），可手動覆寫，離線用 cache
- **更新**：手動按鈕，不自動跳通知
- **圖片**：canvas 壓縮 480px JPEG 存 base64
- **localStorage**：純本地、不上傳；同 origin 多檔可共用 key

## 未來旅遊專案複用
此專案架構已封裝成「旅遊規劃」Skill（`.claude/skills/travel-planning/SKILL.md`）。
新旅遊專案（如濟州島）有兩種起手式：
1. 複製 `.claude/skills/travel-planning/` 到新 repo，套用 skill
2. 直接複製 `group.html` / `index.html` 當起點，替換內容/品牌/幣別

詳見 SKILL.md 的建置 SOP 與 gotchas。

---
© 2026 Muni Co., Ltd. · 本手冊僅供員工旅遊參加者與家屬使用
