# AGENTS.md — Claude Design System 維護指南

## 專案概述

Claude 視覺系統（claude.ai App + claude.com 官網，內容整併於各章）的逆向整理，包含 design tokens、元件規格與一個展示用文件網站。這是**純靜態站**——所有頁面皆為純 HTML，無任何伺服器端依賴，直接用瀏覽器開啟 `index.html`（`file://` 協定）即可瀏覽，不需要 web server 或 localhost。可直接發佈到 GitHub Pages。

## 專案結構

```
Claude-Design-System/
├── AGENTS.md            ← Codex 維護指南（本檔）
├── CLAUDE.md            ← Claude 維護指南
├── README.md            ← 對外說明
├── index.html           ← 文件網站（單檔；版面 1:1 仿 claude.ai app shell——側欄同底色
│                           + border-r-0.5 髮絲線 + ghost 導覽項 + 右上浮動工具列 +
│                           max-w-3xl 內容欄 + 可收合側欄；含投影片模式與亮暗切換）
├── robots.txt           ← 搜尋引擎爬蟲規則（GitHub Pages root）
├── sitemap.xml          ← GitHub Pages sitemap（GitHub Pages root）
├── DESIGN_SYSTEM.md     ← 完整規格的單檔版本（docs/ 的合併鏡像）
├── tokens.css           ← App（claude.ai）樣式表（index.html 引用）
├── tokens-marketing.css ← 行銷官網（claude.com/Webflow）樣式表（index.html 各章「官網」段落用）
├── tokens.json          ← 機器可讀 tokens（HSL + HEX）
├── docs/                ← 分章 Markdown（內容的 source of truth）
│   ├── INDEX.md         ← 目錄（版本號在這裡）
│   ├── 01-theme-architecture.md … 11-implementation.md
│   └── appendix-sources.md
├── assets/              ← SEO cover + SVG（spark、wordmark）+ icons/（28 個 App 圖示）+ marketing/（86 個官網資產）
├── raw/                 ← claude.ai production 原始 CSS 存證（勿改）
└── raw/live-2026-06/    ← live 抓取的 token dump + SVG（v2.0 主要證據，勿改）
```

## 關鍵概念

- **Token 慣例**：顏色以「HSL 三元組」存 CSS 變數（如 `--bg-100: 48 33.3% 97.1%`），取用時 `hsl(var(--bg-100) / alpha)`。主題由 `<html data-theme="claude" data-mode="light|dark|auto">` 切換。
- **三層同步關係**：`docs/`（章節內容）↔ `DESIGN_SYSTEM.md`(單檔鏡像) ↔ `index.html`（網站呈現）。改規格時三處都要動；色碼還要同步 `tokens.css`、`tokens.json` 與 `index.html` 內的 `SWATCH` 物件（色票複製用的 HEX 對照表）。
- **index.html 的 SVG 是直接內嵌的**（從 assets/ 注入），改 assets/ 的 SVG 不會自動反映到網站，需手動同步。
- **raw/ 是證據不是源碼**：來自 Wayback Machine 的 production CSS，只讀不改。

## 網站更新流程

1. `git diff docs/` 確認哪些章節內容變了
2. 對應到 `index.html` 內的 slide（每章一個 `<div class="slide" data-num="01..11|A1">`）
3. 更新 slide 內容（單檔站，所有內容都內嵌）；若改了色彩 token，同步更新 `tokens.css`、`tokens.json`、`index.html` 的 `SWATCH` 物件
4. 更新 `docs/INDEX.md` 的版本號與日期
5. `open index.html` 目視驗證（亮暗都切一次）
6. `git add` + `git commit`（conventional commit 前綴）

## 驗證方式

純靜態站：`open index.html` 直接用瀏覽器開啟即可，**不要**起 localhost server。檢查重點：側欄 14 個章節可切換、🌙 亮暗切換後色票 HEX 值會跟著變、色票點擊可複製、動效章節的格子點擊會重播動畫、鍵盤 ←/→/D/Esc 有效。

## 工作慣例

- 語言：繁體中文（技術名詞保留英文）
- Commit 前綴：`feat:` / `fix:` / `docs:` / `style:`
- 版本號位置：`docs/INDEX.md` 開頭（同步 `index.html` 側欄 `.version`）
- 改動後務必更新 INDEX.md 的版本與日期
- 發佈 GitHub Pages 時 repo 建議名 `claude-design-system`（小寫），description 聚焦 design tokens 與 component reference
