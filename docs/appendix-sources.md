# 附錄：資料來源與檔案

> 所屬文件：[INDEX](./INDEX.md)

---

| 檔案 | 內容 |
|---|---|
| `tokens.css` | 可直接引用的完整變數 + 元件樣式表 |
| `tokens.json` | 機器可讀 token（W3C Design Tokens 風格） |
| `raw/` | 自 production 取回的原始 CSS（主題檔、Button 模組等），供查證 |

**Live 抓取（2026-06-12，v2.0 主要來源）**：Chrome 登入狀態實地抓取 claude.ai（build `e9c073951b`）——`/new`、`/recents`、`/projects`、對話頁、composer 各選單；完整 token dump 與 SVG 存於 `raw/live-2026-06/tokens-and-svgs.json`、圖示成品於 `assets/icons/`。

歷史 production bundles（2026-01 版基礎）：`6492ae367fc05efc.css`（主題 2026-01）、`da25616fcf863192.css`（主題 2025-11）、`d471dc031853b581.css`（行銷 Tailwind 262KB）、`4679f5511a5b0b9e.css`（App Tailwind 229KB）、`f614b4fc9a5ddba6.css` / `b3152fa1bdafab10.css`（Button 模組）、`c2fb8af2f7b87066.css`（字體）、`d6bd087f08a7f52f.css`（JetBrains Mono）、`6c432c275096a856.css`（navbar 簾幕）、`7bf97b7f00340a78.css`（Toast）、`acdaad1d23646914.css`（KaTeX）。
