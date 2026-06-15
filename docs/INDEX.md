# Claude Design System — 文件目錄

> **版本**：v3.7 | **最後更新**：2026-06-15
> 範圍：claude.ai App 的 token、shell、composer、設定與功能頁；claude.com marketing/docs 的色彩、排印、元件、頁型與品牌資產；production CSS 存檔作為穩定性對照。

---

## 文件結構

| 編號 | 文件 | 說明 |
|------|------|------|
| 01 | [主題架構](./01-theme-architecture.md) | 雙站總覽（App vs 官網）、data-theme/mode、HSL 變數慣例 |
| 02 | [色彩系統](./02-color-system.md) | App primitive→語意雙層 + 官網 swatch/8 區段主題 |
| 03 | [字體排印](./03-typography.md) | Anthropic 三字體、字重補償 + 官網流式字級 |
| 04 | [佈局](./04-layout.md) | 斷點、z-index、間距 + 官網 mobile-first grid / 流式區段間距 |
| 05 | [圓角・邊框・陰影](./05-shape-elevation.md) | 0.5px 髮絲線、官網 radius/focus token、極淡陰影系統 |
| 06 | [動效系統](./06-motion.md) | Duration / Easing / Keyframes + 官網 Webflow/GSAP motion stack |
| 07 | [元件規格](./07-components.md) | Button、Input、Card、App shell/composer/模型選擇器、Segmented selector、官網全站元件矩陣、Toast、Navbar、Accordion 等 |
| 08 | [圖示與品牌](./08-icons-brand.md) | App 28 個圖示 + 官網 116 個 SVG、星芒、字標 |
| 09 | [內容樣式](./09-content-styles.md) | App Markdown / One Dark + 官網 rich text / docs shell |
| 10 | [頁面結構模式](./10-page-patterns.md) | App 聊天介面骨架 + claude.com route groups / 頁型版面 |
| 11 | [實作指南](./11-implementation.md) | tokens.css / tokens-marketing.css 快速開始、Tailwind config、實作還原要點 |
| 12 | [新 UI 設計推導指南](./12-design-playbook.md) | 遇到未知 UI 時的 surface 判斷、layout/元件推導、狀態矩陣、Do/Don’t、demo 自檢 |

### 附錄

| 編號 | 文件 | 說明 |
|------|------|------|
| A | [資料來源](./appendix-sources.md) | 分析的 production bundle 清單與檔案對照 |

---

## 快速導覽

**想直接動手做？** → [11 實作指南](./11-implementation.md) + 根目錄 `tokens.css`
**想查色碼？** → [02 色彩系統](./02-color-system.md) 或機器可讀的 `tokens.json`
**想還原元件？** → [07 元件規格](./07-components.md) + [06 動效系統](./06-motion.md)
**想設計文件沒有列出的 UI？** → [12 新 UI 設計推導指南](./12-design-playbook.md)
**想要 SVG 資產？** → [08 圖示與品牌](./08-icons-brand.md) + `assets/` 資料夾
**想看視覺成果？** → 開啟根目錄 `index.html`（文件站本身就用此設計系統打造）
