# Claude Design System — 文件目錄

> **版本**：v2.2 | **最後更新**：2026-06-12
> 來源：claude.ai App + claude.com 官網 **雙站 live 抓取**（2026-06-12）+ production CSS 存檔（2025-11 ~ 2026-03）

---

## 文件結構

| 編號 | 文件 | 說明 |
|------|------|------|
| 01 | [主題架構](./01-theme-architecture.md) | 雙站總覽（App vs 官網）、data-theme/mode、HSL 變數慣例 |
| 02 | [色彩系統](./02-color-system.md) | App primitive→語意雙層 + 官網 swatch/8 區段主題 |
| 03 | [字體排印](./03-typography.md) | Anthropic 三字體、字重補償 + 官網流式字級 |
| 04 | [佈局](./04-layout.md) | 斷點、z-index、間距 + 官網 12 欄格線/流式區段間距 |
| 05 | [圓角・邊框・陰影](./05-shape-elevation.md) | 0.5px 髮絲線、圓角階層、極淡陰影系統 |
| 06 | [動效系統](./06-motion.md) | Duration / Easing / 17 組 Keyframes / 微動效 |
| 07 | [元件規格](./07-components.md) | Button、Input、Card、Toast、Navbar、Accordion 等 |
| 08 | [圖示與品牌](./08-icons-brand.md) | App 28 個圖示 + 官網 86 個 SVG、星芒、字標 |
| 09 | [內容樣式](./09-content-styles.md) | 程式碼高亮（One Dark）、Markdown、KaTeX |
| 10 | [頁面結構模式](./10-page-patterns.md) | App 聊天介面骨架 + 官網 45 頁站點與版面 |
| 11 | [實作指南](./11-implementation.md) | tokens.css 快速開始、Tailwind config、還原要點 |

### 附錄

| 編號 | 文件 | 說明 |
|------|------|------|
| A | [資料來源](./appendix-sources.md) | 分析的 production bundle 清單與檔案對照 |

---

## 快速導覽

**想直接動手做？** → [11 實作指南](./11-implementation.md) + 根目錄 `tokens.css`
**想查色碼？** → [02 色彩系統](./02-color-system.md) 或機器可讀的 `tokens.json`
**想還原元件？** → [07 元件規格](./07-components.md) + [06 動效系統](./06-motion.md)
**想要 SVG 資產？** → [08 圖示與品牌](./08-icons-brand.md) + `assets/` 資料夾
**想看視覺成果？** → 開啟根目錄 `index.html`（文件站本身就用此設計系統打造）
