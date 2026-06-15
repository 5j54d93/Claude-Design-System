# 11. 實作指南

> 所屬文件：[INDEX](./INDEX.md)

---

### 11.1 快速開始

```html
<html data-theme="claude" data-mode="auto" class="antialiased scroll-smooth">
<head><link rel="stylesheet" href="tokens.css"></head>
<body><!-- body 自動套 bg-bg-100 text-text-100 font-ui --></body>
</html>
```

`tokens.css`（本資料夾附）含：全部色彩變數（claude 亮/暗 + console）、語意字族、字重、type styles、z-index、陰影、keyframes、Button/Input/Card/焦點環等元件類別。

### 11.2 Tailwind 對應設定

```js
// tailwind.config.js — 與 claude.ai 相同的接法
const c = (name) => `hsl(var(--${name}) / <alpha-value>)`;
module.exports = {
  darkMode: ['selector', '[data-mode="dark"]'],
  theme: {
    extend: {
      colors: {
        'bg-000': c('bg-000'), 'bg-100': c('bg-100'), 'bg-200': c('bg-200'),
        'bg-300': c('bg-300'), 'bg-400': c('bg-400'), 'bg-500': c('bg-500'),
        'text-000': c('text-000'), 'text-100': c('text-100'), 'text-200': c('text-200'),
        'text-300': c('text-300'), 'text-400': c('text-400'), 'text-500': c('text-500'),
        'border-100': c('border-100'), 'border-200': c('border-200'),
        'border-300': c('border-300'), 'border-400': c('border-400'),
        'accent-brand': c('accent-brand'),
        'accent-main-000': c('accent-main-000'), 'accent-main-100': c('accent-main-100'),
        'accent-main-200': c('accent-main-200'),
        'accent-secondary-000': c('accent-secondary-000'),
        'accent-secondary-100': c('accent-secondary-100'),
        'accent-secondary-900': c('accent-secondary-900'),
        'accent-pro-000': c('accent-pro-000'), 'accent-pro-100': c('accent-pro-100'),
        'accent-pro-200': c('accent-pro-200'), 'accent-pro-900': c('accent-pro-900'),
        'danger-000': c('danger-000'), 'danger-100': c('danger-100'), 'danger-900': c('danger-900'),
        'success-100': c('success-100'), 'warning-100': c('warning-100'),
        'oncolor-100': c('oncolor-100'),
        'always-white': c('always-white'), 'always-black': c('always-black'),
      },
      borderWidth: { '0.5': '0.5px', '1.5': '1.5px' },
      zIndex: { header: 20, sidebar: 30, modal: 40, dropdown: 50, overlay: 50, tooltip: 50, toast: 60 },
      fontFamily: {
        ui: 'var(--font-ui)', 'ui-serif': 'var(--font-ui-serif)',
        'claude-response': 'var(--font-claude-response)', mono: 'var(--font-mono)',
      },
    },
  },
};
```

### 11.3 還原視覺的 10 個關鍵

1. **米白不是白**：頁面底是 `#faf9f5`（暖米白），卡片才是純白 — 暗模式底是 `#262624`。
2. **AI 內容用襯線、UI 用無襯線**，標題（display/title/heading）也是襯線。
3. **0.5px 髮絲邊框 + 深色低透明**（`border-200/30%`），絕少用實色灰框。
4. **暗模式調降字重**（600→530 等），不只換色。
5. **陰影極輕**（黑 4–8%），強調卡用「彩色擴散光暈」（`0 4px 24px` + accent 色）。
6. 按鈕按壓 `scale(0.985)`、hover 不變底色就變字色，全部 `100~200ms`。
7. **藍色是互動色**（focus ring `accent-secondary/70%`、::selection 藍底白字、連結），橘色是品牌與 CTA。
8. 圓角階層明確：按鈕 8–9.6px、輸入 9.6px、容器 16px、卡片 24px。
9. 程式碼區塊永遠是 One Dark 深色島（亮模式也深底）。
10. Dropdown/modal 標配 `fade+zoom(.95)`；行銷頁下拉用 `clip-path` 簾幕；載入用 shimmer。

### 11.4 字體替代建議（因官方字體不可外用）

| 角色 | 最接近 | 開源替代 |
|---|---|---|
| anthropicSans | Styrene B | Inter（調 `font-feature-settings`）/ Public Sans |
| anthropicSerif | Tiempos Text / Copernicus 系 | Source Serif 4（有 opsz 軸，**推薦**）/ Lora |
| 等寬 | JetBrains Mono（本身開源 ✓） | — |

### 11.5 官網規格實作（claude.com）

若目標是還原 claude.com，而不是 claude.ai App，請改引用 `tokens-marketing.css` 的官網類別與變數：

```html
<link rel="stylesheet" href="tokens-marketing.css">
<section class="mkt-section mkt-theme-light">
  <div class="mkt-container">
    <a class="mkt-button mkt-button-brand" href="#">Get started</a>
  </div>
</section>
```

實作重點：

1. 用 `--swatch--*` 與 `.mkt-theme-*` 做區段換膚，不用 `data-mode="dark"` 模擬全站暗色。
2. 版面採 mobile-first：預設 6 欄 / 1rem gutter，桌面 section 再升到 12 欄 / 2rem。
3. 大標使用 `--_typography---font-size--display-*` / `h*` 的 `clamp()`，不要用 viewport-width 直接縮放文字。
4. CTA hover 以 border/spacer 變化為主，brand button 底色維持 `clay-interactive`。
5. docs 頁型要另外處理：`/docs/*` 是 Next/Tailwind shell，不套 Webflow shared CSS。

### 11.6 實作檢查清單

1. 先判斷 surface：App / Marketing / Docs / Hybrid，不要混用兩站的互動模型。
2. App 介面使用 `tokens.css`、`data-theme="claude"`、`data-mode="auto|light|dark"` 與 HSL semantic token。
3. 官網頁面使用 `tokens-marketing.css`、`.u-theme-*` 區段主題、流式 typography 與 mobile-first grid。
4. 新元件先套用既有 anatomy：低透明髮絲框、8–12px 控制圓角、16–24px 卡片/容器圓角、100–200ms 互動。
5. Docs 類頁型另用左 nav + 內容欄 + 右 TOC；code/table/callout/copy controls 優先保持可掃描與可操作。
6. 未列出的 UI 先走 §12 的推導流程，再回到 §2–§10 對照 token、layout、component、motion 與 accessibility。
