---
name: travel-planning
description: >-
  旅遊規劃 — 建立行動裝置優先的「旅遊行程手冊網頁 App」（單一 HTML 檔 + GitHub Pages 部署，
  純前端無後端，可離線使用）。當使用者要做旅遊行程表、團體/家族/員工旅遊手冊、把一份旅行社行程
  變成互動式網頁、或需要含匯率計算/記帳/退稅清單/代買清單/咖啡與購物指南的行程 App 時使用。
  支援個人版與團隊版雙檔、即時匯率、localStorage 個人資料、PWA 加到主畫面。
---

# 旅遊規劃（Travel Planning）

建立一個 mobile-first 的旅遊行程手冊網頁 App。單一 `.html` 檔、純前端、
部署到 GitHub Pages，使用者加到主畫面即可離線用。此 skill 是經實戰
（九州 5 日員工旅遊）淬煉出的可重用藍圖。

## 何時使用
- 使用者貼旅行社 PDF / 行程表，要做成好用的網頁
- 要做團體旅遊手冊分享給同行的人
- 需要行程 + 匯率 + 記帳 + 退稅 + 購物清單整合在一頁
- 要可離線、加到手機主畫面當 App 用

## 開場要先問清楚
1. **誰用**：個人自己用 / 團體分享 / 兩者都要（決定要不要拆 `index.html` 個人版 + `group.html` 團隊版）
2. **品牌/視覺**：有沒有 logo、主色偏好（決定配色 tokens）
3. **目的地與幣別**：決定匯率 API 的目標貨幣（JPY→TWD、KRW→TWD 等）
4. **部署**：GitHub Pages（最簡單，main 分支自動部署）

## 技術架構（不要偏離）
- **單一 HTML 檔**：HTML + 內嵌 CSS（無框架）+ 原生 JS（無 jQuery/React）
- **部署**：GitHub Pages，push main 自動部署，網址 `https://<user>.github.io/<repo>/`
- **資料儲存**：`localStorage` 純本地（記帳、退稅打勾、代買清單、匯率），不上傳任何伺服器
- **離線**：所有東西都在單檔內，加到主畫面後可離線
- **更新機制**：改檔 push → 使用者按 🔄 按鈕 reload（不要做會一直跳的自動通知）

## 分頁結構（依需求增減）
sticky header（永遠固定在頂部）內含：
- 品牌列（logo + 名稱 + 行程 tag）
- 匯率計算機（輸入外幣 → 即時換算台幣）
- 下次集合倒數（讀 GATHERS 陣列，每 30 秒更新）
- 🔄 強制刷新按鈕

分頁（橫向滾動 tabs + section 切換 + 左右滑手勢）：
- 📅 行程（5 天時間軸，當日自動展開，今日 highlight）
- 🛏️ 起床（每天建議起床/早餐時間）
- 🆓 自由（自由時段景點玩法）
- 🛍️ 購物（商場品牌目錄）
- 🍵 特產主題（如抹茶採購；視目的地換）
- ☕ 咖啡（每天精選 1-2 家）
- 🧾 退稅（每店 checklist + 樓層/櫃台位置）
- 💴 記帳（KPI + 新增/編輯/刪除 modal，現金/刷卡分開、每筆顯示台幣）
- 🛒 我的代買（圖片上傳 + 數量 + 代買對象 + 台幣換算）
- ℹ️ 資訊（緊急聯絡、飯店、當地小資訊、匯率設定、版權聲明）

## 可重用元件（CSS class / JS 函式）
- `.sticky` / `.brand` / `.next-gather`：頂部固定區
- `.cover`：首頁封面卡（logo + 品牌故事 + 行程 + quote，可加水彩 SVG 角飾）
- `.tabs` / `.tab` + `.section`：分頁切換
- `.day` / `.day-head` / `.event`：行程時間軸（可收合）
- `.card` / `.row` / h3/h4：通用內容卡
- `.kpi` / `.kpi-card`：統計卡
- `.modal` / `.modal-box`：底部彈出表單
- `.collapsible`：可收合卡片（h3 點擊 toggle + localStorage 記憶）
- 記帳 CRUD：openModal(idx) / saveSpend / delSpend / editSpend
- 代買 CRUD + 圖片壓縮：compressImage（canvas 縮到 480px JPEG q=0.72 存 base64）
- 匯率：loadRate / fetchLiveRate / setManualRate / applyRate
- copyText()：一鍵複製外語短語（含 execCommand fallback）

## 設計風格
- mobile-first、大字、留白、圓角、柔和陰影
- 配色用 CSS 變數或固定 tokens；依品牌調主色
- 字體：標題可用 Georgia/Noto Serif 襯線；數字用 tnum
- 集合時間用米色底（`.event.gather`）標示，最醒目

## 建置 SOP
1. 確認個人/團隊需求、品牌、幣別
2. 從本 repo 的 group.html / index.html 複製為起點（最快）
3. 全域替換：localStorage key 前綴（如 hiyp- → jeju-）、APP_VERSION、RATE 目標幣別
4. 替換內容：GATHERS 陣列、每天行程 events、飯店資訊、緊急聯絡、商場/咖啡/退稅資料
5. 替換品牌：logo（img src + SVG fallback）、配色 tokens、標題、favicon
6. 逐分頁檢查無上一個專案殘留
7. 啟用 GitHub Pages（main 分支），分享網址

## 關鍵 GOTCHAS（踩過的雷，務必照做）
1. **時區一律用 Date.UTC 手動算**，不要靠 `new Date('...+09:00')` 字串解析
   （iOS Safari 會回 Invalid Date）。GATHERS 每筆存 `{dt,tz:'+09:00',tzLabel}`，
   顯示用 wall-clock 時間 + 時區標籤，倒數用絕對時間差。
   跨時區行程（如台灣集合 +08、當地行程 +09）每筆標各自時區。
2. **「今日」判斷用本地日期**，不要 `toISOString()`（那是 UTC，UTC+8 半夜會差一天）。
   寫 `localDateStr()` 用 getFullYear/getMonth/getDate。
3. **Cache-Control meta tags** 三件套（no-cache/no-store/must-revalidate）放 head，
   減少 PWA 快取頑固。
4. **更新機制給按鈕、不要自動跳通知**（會煩）。sticky header 放 🔄，資訊頁放強制刷新鈕。
   每次改版手動 bump APP_VERSION。
5. **匯率即時抓 + 手動覆寫**：開 app fetch `https://open.er-api.com/v6/latest/<幣別>`
   （免金鑰、CORS OK、含 TWD），cache 到 localStorage，離線沿用；保留手動輸入欄位
   （收款建議手動 +3-5% 因銀行有價差）。RATE 用 let，更新後重算所有換算顯示。
6. **localStorage 純本地、不上傳**：要跟使用者說清楚——程式根本沒有上傳碼，不是權限問題。
   同 origin 下多個 HTML 檔共用同一組 key 可讓資料互通（如匯率）；要隔離就用不同前綴。
7. **圖片上傳**用 canvas 壓縮再存 base64（480px / JPEG 0.72，每張約 30-50KB），
   避免塞爆 localStorage（上限約 5MB）。
8. **不要創 logo 版權問題**：用真實 logo 圖檔（img）或自繪 SVG fallback；
   `onerror` 多段 fallback：logo.png → logo.svg → inline SVG。
9. **景點介紹白話化**：加「坐小火車超可愛、拍美照」這種生活感註解，不要乾巴巴跟團文。
10. **餐點標起訖時間**（如 18:00-19:15 buffet），讓人推算「幾點吃完去逛」；
    自助餐註明「自由時間 = 用餐 + 出去逛，自己抓」。

## 部署/Git
- 開發分支開發 → 合併 main → GitHub Pages 自動部署 1-2 分鐘
- fast-forward merge：`git merge --ff-only <branch>`
- 每次有意義的變更就 commit，訊息寫清楚「為什麼」

## 參考實作
本 repo（kyushuV2）的 `group.html`（團隊版）與 `index.html`（個人版）即完整範例，
可直接複製當新專案起點。
