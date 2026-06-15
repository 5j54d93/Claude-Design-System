# 01. 主題架構

> 所屬文件：[INDEX](./INDEX.md)

---

claude.ai 的主題由 `<html>` 上的兩個 data attribute 控制：

```html
<html class="h-screen antialiased scroll-smooth"
      lang="en-US"
      data-theme="claude"   <!-- 主題：claude（主站）/ console（開發者後台深色主題） -->
      data-mode="auto">     <!-- 模式：light / dark / auto（auto 跟隨 prefers-color-scheme） -->
<body class="bg-bg-100 text-text-100 font-ui min-h-screen">
```

**核心機制**：所有顏色 token 以「HSL 三元組」（不含 `hsl()` 包裝）存於 CSS 變數，使用時以 `hsl(var(--token) / alpha)` 組合，讓任何顏色都能直接調透明度：

```css
/* 定義 */
--bg-100: 48 33.3% 97.1%;
/* 使用 */
background-color: hsl(var(--bg-100));            /* 不透明 */
border-color: hsl(var(--border-200) / 30%);      /* 30% 透明度 — 邊框慣用 */
```

> ⚠️ 這是整個系統最重要的慣例：**邊框幾乎從不使用不透明色**，而是深色 token + 低透明度（詳見 §5）。

### 1.1 雙站架構總覽（App vs 行銷官網）

Claude 的視覺系統落在兩個站：**claude.ai（App，Next.js）** 與 **claude.com（行銷官網，Webflow）**。同一套品牌語言（Anthropic 三字體、clay 橘、暖灰階、襯線標題），但機制不同：

| 面向 | App（claude.ai） | 行銷官網（claude.com） |
|---|---|---|
| Token 架構 | primitive `--_*`（317 個）→ 語意層 | swatch 色板 → `.u-theme-*` 區段主題 |
| 主題切換 | `data-theme` + `data-mode` 全站 | 無全站深色，**逐區段換膚**（8 種主題） |
| 頁面底色 | `#f8f8f6`（2026-06 起） | **`#faf9f5` 經典米白** |
| 字級 | 固定 rem | **全流式 `clamp()`**（320→1440px 內插） |
| `::selection` | 藍底白字 | **clay 橘 50% 透明** |
| 按鈕 hover | 換底色 | **邊框加倍** |

之後各章中標註「**官網**」的小節即 claude.com 專屬規格；未標註者為 App / 共通。

### 1.2 claude.com 架構補充

claude.com 的公開內容可分成 Webflow 主站、Next/Tailwind 文件頁與多語系模板三類；它們共享同一套品牌語言，但樣式來源與實作重點不同：

| 區塊 | 技術/樣式來源 | 設計系統涵蓋方式 |
|---|---|---|
| `claude.com` 主站 Webflow 頁 | `claude-brand.shared.9ce205edd.min.css`，共 `203` 個 CSS 變數、`34` 個 swatch、Webflow component variants | 色彩、字級、佈局、圓角、按鈕、頁型分別落在 §2–§7、§10 |
| `claude.com/docs/*` 文件頁 | Next/Tailwind shell；不套用 Webflow shared CSS | 以 docs shell、TOC、callout、table、copy/floating controls 歸入 §7、§9、§10 |
| 多語系 `/de` `/fr` `/ja` `/ko` | 同模板內容變體，主要差異是文字長度與 locale path | 不重複列元件；實作時需保留 label wrapping 與彈性寬度 |
