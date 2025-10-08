# 啟德體育園活動資訊儀表板 (Kai Tak Sports Park Events Dashboard)

一個即時顯示啟德體育園活動資訊的數碼儀表板，提供活動日程、設施狀態和交通資訊。本專案包含前端展示界面和相關的數據抓取腳本。

## 主要功能

- 📅 今日活動顯示
  - 自動篩選並展示當天進行的活動
  - 清晰顯示活動時間、地點和類型
  - 支援多種日期格式：
    - 單日：「2025年10月5日」
    - 跨年份日期範圍：「2025年9月27日至2026年10月13日」
    - 同年跨月日期範圍：「2025年9月27日至10月13日」
    - 同月日期範圍：「2025年10月4至7日」
    - 同月多日：「2025年10月14及15日」

- 🎯 最新活動列表
  - 展示所有即將舉行的活動
  - 按時間順序排列
  - 包含活動詳細資訊和購票連結

- 🏟️ 設施資訊
  - 即時顯示各場館開放狀況
  - 包含容納人數和開放時間
  - 清晰標示公眾使用限制

- 🚇 交通資訊
  - 港鐵路線建議
  - 巴士路線資訊
  - 停車場位置指引
  - 的士站位置

## 資料夾結構
- `exported-assets/` — Static frontend assets and scraping scripts
  - `index.html` — Main static dashboard UI (Chinese/Traditional) that fetches event data from a public JSON
  - `app.js` — Frontend JavaScript for UI interactions, clock, animations, and fetching `total_events.json` from a GitHub raw URL
  - `style.css` — Styles for the dashboard (included in `exported-assets`)  
  - `latest_events.json` — Output sample from the scraper with brief event listings
  - `total_events.json` — Output sample with detailed events collected by the scraper
  - `selenium_events.py` — Selenium-based scraper that visits the official events pages and writes `latest_events.json` / `total_events.json`
  - `app -old.js` — Older copy of the frontend script
  - `index.html` — main dashboard
  - `event_example.txt` — sample event data/text

- `n8n-puppeteer-setup/` — Dockerfile and supporting resources for an n8n image with Puppeteer (Chromium) installed
  - `Dockerfile` — Builds Node-based image with n8n and Puppeteer integration

## 技術特點

### 前端功能
- ⚡ 即時數據更新（每5分鐘自動刷新）
- 🕒 實時時鐘顯示（香港時間）
- 📱 響應式設計，支援各種裝置
- ♿ 無障礙設計，支援鍵盤操作
- 🎯 平滑動畫效果
- 🌐 完整支援中文日期格式和時間顯示
- 🔄 自動切換本地/遠端資料來源
- ⚠️ 完整的錯誤處理和診斷功能

### 資料處理
- 強大的中文日期解析：
  - 單日期：「YYYY年MM月DD日」
  - 跨年份範圍：「YYYY年MM月DD日至YYYY年MM月DD日」
  - 同年跨月範圍：「YYYY年MM月DD日至MM月DD日」
  - 同月範圍：「YYYY年MM月DD至DD日」
  - 同月多日：「YYYY年MM月DD及DD日」
  - 支援日期待定/TBD
- JSON 錯誤處理和診斷功能
- JSON 格式儲存活動資訊
- 本地/遠端資料源自動切換

## 快速開始
1. Open `exported-assets/index.html` in a browser. It's a static HTML file and does not require a server for basic viewing. Some browsers block cross-origin fetches for local files — if the events JSON is remote, open the page via a simple local server.

- Using Python 3 (recommended) to serve locally:

```powershell
# from repository root
cd "c:\Users\Lo Ka\Documents\exported-assets"
python -m http.server 8000; # then open http://localhost:8000 in your browser
```

- Using Node (http-server):

```powershell
npm install -g http-server
cd "c:\Users\Lo Ka\Documents\exported-assets"
http-server -p 8000
```

## 使用說明

### 資料來源設定
- 預設從 GitHub 讀取資料：`https://raw.githubusercontent.com/ryoohkilo/kai-tak-events-scraper/main/total_events.json`
- 如需使用本地資料，請修改 `app.js` 中的 URL 為 `./total_events.json`

### 瀏覽器支援
- ✅ Google Chrome（建議）
- ✅ Mozilla Firefox
- ✅ Microsoft Edge
- ✅ Safari

### 事件抓取器使用說明
Requirements:
- Python 3.8+
- pip packages: selenium, webdriver-manager
- Chrome or Chromium installed on the machine

Install dependencies (PowerShell):

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

If you don't have a `requirements.txt`, install directly:

```powershell
pip install selenium webdriver-manager
```

Run the scraper (from `exported-assets`):

```powershell
cd "c:\Users\Lo Ka\Documents\exported-assets"
python selenium_events.py
```

The script will:
- Launch Chrome via webdriver-manager
- Visit the Kai Tak events listing
- Collect basic event links and write `latest_events.json`
- Visit each event and extract details into `total_events.json`

Dockerfile — n8n + Puppeteer setup
The `n8n-puppeteer-setup/Dockerfile` builds an image with Node 16, installs n8n, Chromium and Puppeteer-related dependencies, and includes a healthcheck. It expects an `N8N_VERSION` build-arg when building.

Build example (PowerShell):

```powershell
cd "c:\Users\Lo Ka\Documents\n8n-puppeteer-setup"
docker build --build-arg N8N_VERSION=1.0.0 -t n8n-puppeteer:local .
```

Replace `1.0.0` with the desired n8n version. The Dockerfile installs `n8n-nodes-puppeteer` into the global `n8n` installation.

Tips & troubleshooting
- If the frontend fetch to GitHub raw returns CORS or 404, either host `total_events.json` on a permissive host or use a local copy and update `app.js`.
- Selenium: If Chrome can't be found, install Chrome/Chromium or point Selenium to the correct binary.
- Docker: The Dockerfile uses Alpine packages and installs Chromium. If you see missing fonts or rendering issues in Puppeteer, ensure the required fonts are installed or add additional font packages.

Next steps (suggested)
- Add a `requirements.txt` for the Python scraper to make setup simpler.
- Add a small `package.json` if you'd like to serve the static site with npm scripts or run a lightweight build step.
- Add a `README` inside `exported-assets/` with local-run tips specific to the dashboard.

License
- No license file was found in the workspace. If you plan to publish or share this repository, add a `LICENSE` file with your chosen license.

Contact / Questions
If you'd like, I can:
- Update `app.js` to default to the local `total_events.json` and add an environment toggle
- Add `requirements.txt` and a tiny PowerShell script to run the scraper
- Create a `docker-compose.yml` for the n8n image and a static-file server

