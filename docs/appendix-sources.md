# 附錄：資料來源與檔案

> 所屬文件：[INDEX](./INDEX.md)

---

| 檔案 | 內容 |
|---|---|
| `tokens.css` | 可直接引用的完整變數 + 元件樣式表 |
| `tokens.json` | 機器可讀 token（W3C Design Tokens 風格） |
| `raw/` | production 原始 CSS 參考檔（主題檔、Button 模組等） |

**claude.ai App（2026-06）**：App surface 包含 shell、聊天輸入卡、模型選擇器、設定 modal、功能頁與對話內容樣式；token 與 SVG 參考檔為 `raw/live-2026-06/tokens-and-svgs.json`，圖示成品位於 `assets/icons/`。

**claude.ai App 結論（2026-06）**：語意 token 與字體鏈維持穩定；`--accent-secondary-*` 已由 `--accent-*` 取代。App shell 使用 `288px` 側欄、`bg-100` 同底、`0.5px` 低透明分隔；composer 使用 `20px` 圓角、ring + 柔陰影；模型選擇器使用 `12px` popover 與三層陰影。UI chrome 圖示主要由 `Anthropicons-Variable` 圖示字型渲染，內嵌 SVG 只保留在字標、星芒、語音、chips 與少數特殊圖示。

**claude.com Marketing / Docs（2026-06）**：主站由 Webflow shared CSS `claude-brand.shared.9ce205edd.min.css` 定義 swatch、排印、grid、component variants 與 motion layer；`/docs/*` 另採 Next/Tailwind docs shell。公開站點包含 `1,520` 個 canonical marketing pages、`75` 個 docs pages 與多語系模板變體。

**claude.com 圖示結論**：官網共用 UI 圖示以 `currentColor` 單色 SVG 為主，可跟隨區段主題變色；`assets/marketing/` 收錄共用 UI 圖示、產品 pictogram、星芒插圖、社群圖示與 Claude Code lockup。單頁第三方品牌商標不納入 Claude 設計系統圖示範圍。

歷史 production bundles（2026-01 版基礎）：`6492ae367fc05efc.css`（主題 2026-01）、`da25616fcf863192.css`（主題 2025-11）、`d471dc031853b581.css`（行銷 Tailwind 262KB）、`4679f5511a5b0b9e.css`（App Tailwind 229KB）、`f614b4fc9a5ddba6.css` / `b3152fa1bdafab10.css`（Button 模組）、`c2fb8af2f7b87066.css`（字體）、`d6bd087f08a7f52f.css`（JetBrains Mono）、`6c432c275096a856.css`（navbar 簾幕）、`7bf97b7f00340a78.css`（Toast）、`acdaad1d23646914.css`（KaTeX）。
