# 附錄：資料來源與檔案

> 所屬文件：[INDEX](./INDEX.md)

---

| 檔案 | 內容 |
|---|---|
| `tokens.css` | 可直接引用的完整變數 + 元件樣式表 |
| `tokens.json` | 機器可讀 token（W3C Design Tokens 風格） |
| `raw/` | 自 production 取回的原始 CSS（主題檔、Button 模組等），供查證 |

**Live 抓取（2026-06-12，v2.0 主要來源）**：Chrome 登入狀態實地抓取 claude.ai（build `e9c073951b`）——`/new`、`/recents`、`/projects`、對話頁、composer 各選單；完整 token dump 與 SVG 存於 `raw/live-2026-06/tokens-and-svgs.json`、圖示成品於 `assets/icons/`。

**Live 複核（2026-06-15，v3.0）**：以 Claude in Chrome 於登入狀態（Pro）重新進入 `claude.ai/new`，用 DOM `getComputedStyle` 直接讀取 `:root` 變數——`--bg-*`、`--text-*`、`--border-*`、`--accent-brand`、`--brand-*`、`--accent-*`(藍)、`--accent-pro-*`、`--danger/success/warning-*`、字族鏈皆與 §2.0／§3.0 的 2026-06-12 值**完全一致**，確認該批 token 仍為現行。同時確認 `--accent-secondary-*` 在 live 已為空（更名為 `--accent-*`）。並實測補錄 App shell（側欄 `288px`／`bg-100`／`0.5px border 15%`）、composer（`radius 20px` + ring/陰影）、模型選擇器 popover（`radius 12px` + 三層陰影、模型清單 Fable 5／Opus 4.8／Sonnet 4.6／Haiku 4.5）與 Products 區（Code、Design）、Use incognito 等 2026-06 介面變化，見 §7.17–7.19、§5.3。並進一步走訪 **Settings modal**（`#settings/*`、`960×720`、左 nav rail + 設定列 pattern，§7.20）與 **功能頁**（Projects/Artifacts 的 list/collection + empty state、Customize 的 `49px` rail + `255px` 次級面板，§10.2），補入 App 非聊天頁的版面模式。**圖示系統重大發現**：實測 `data-cds="Icon"` 元件在頁面上 `66/66` 全為**可變圖示字型 `Anthropicons-Variable`** 的 PUA glyph（非內嵌 SVG），全文件僅 13 個 inline `<svg>`；故設定/導覽等新圖示無法以 DOM 擷取成 SVG 檔，已於 §8.0.1 以「記錄圖示系統架構與來源」方式收錄。

**Full sitemap crawl（2026-06-15）**：`claude.com/robots.txt` 允許全站並列出 `sitemap.xml` + `docs/sitemap.xml`；主 sitemap 共 `3,125` URLs，canonical `1,520`、多語系 `1,605`，docs sitemap `75`。本輪實際下載 canonical + docs 共 `1,595` 頁，狀態碼 `1595/1595 = 200`，HTML 分析量約 `761.6 MB`，並解析最新 shared CSS `claude-brand.shared.9ce205edd.min.css`（約 `2.63 MB`）。`claude.ai/robots.txt` 禁止 `/new?*`、`/chat/*`、`/settings*`、`/api/*` 等 App 內路徑；`claude.ai/sitemap.xml` 直抓遇到 Cloudflare challenge，因此 App 端仍以 2026-06-12 登入狀態 live 抓取為主證據。

**Sitemap icon sweep（2026-06-15）**：對全部 `1,520` 個 canonical 路徑實抓 `1,516`，抽出所有 inline `<svg>`、以 path 幾何去重，新增 `30` 個跨頁共用（≥3 頁、`currentColor`/單色）的 Claude 設計系統圖示至 `assets/marketing/`（總數 86→116），並排除 68 個單頁第三方品牌商標。詳見 §8.2。

歷史 production bundles（2026-01 版基礎）：`6492ae367fc05efc.css`（主題 2026-01）、`da25616fcf863192.css`（主題 2025-11）、`d471dc031853b581.css`（行銷 Tailwind 262KB）、`4679f5511a5b0b9e.css`（App Tailwind 229KB）、`f614b4fc9a5ddba6.css` / `b3152fa1bdafab10.css`（Button 模組）、`c2fb8af2f7b87066.css`（字體）、`d6bd087f08a7f52f.css`（JetBrains Mono）、`6c432c275096a856.css`（navbar 簾幕）、`7bf97b7f00340a78.css`（Toast）、`acdaad1d23646914.css`（KaTeX）。
