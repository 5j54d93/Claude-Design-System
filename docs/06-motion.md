# 06. 動效系統

> 所屬文件：[INDEX](./INDEX.md)

---

### 6.1 Duration 刻度

`35ms`（瞬時）、`75ms`、`100ms`（按鈕）、`150ms`（預設 transition）、`200ms`（卡片/toast）、`250ms`、`300ms`（accordion）、`500ms`、`600ms`（navbar 簾幕）、`700ms`、`1s`。

### 6.2 緩動曲線（Easing）

| 名稱 | 值 | 用於 |
|---|---|---|
| 標準（Tailwind ease） | `cubic-bezier(.4, 0, .2, 1)` | 預設 transition |
| **Hero 按鈕彈性** | `cubic-bezier(.165, .85, .45, 1)` | CTA hover 縮放 |
| **簾幕（curtain）** | `cubic-bezier(.22, 1, .36, 1)` | navbar 下拉 |
| **Toast 入場** | `cubic-bezier(.16, 1, .3, 1)` | 通知滑入 |
| 過衝回彈 | `cubic-bezier(0, .9, .5, 1.35)` | 彈出強調 |
| 輕微過衝 | `cubic-bezier(.17, .67, .3, 1.2)` | |
| 快出極緩入 | `cubic-bezier(.19, 1, .22, 1)` | 大面積位移 |
| 蓄力衝刺 | `cubic-bezier(.75, 0, 0, 1.15)` | |
| ease-out | `cubic-bezier(0, 0, .2, 1)` | accordion |

`.transition` 預設：`color, background-color, border-color, text-decoration-color, fill, stroke, opacity, box-shadow, transform, filter, backdrop-filter`，`150ms`，標準曲線。

### 6.3 Keyframes 全集

```css
/* 淡入 / 縮放（dropdown、modal 標配：fade + zoom 同時用） */
@keyframes fade { 0% { opacity: 0 } to { opacity: 1 } }
@keyframes zoom { 0% { transform: scale(.95) } to { transform: scale(1) } }

/* 游標閃爍（串流輸出游標） */
@keyframes blink { 0%, to { opacity: 1 } 50% { opacity: 0 } }

/* Skeleton 掃光（搭配 ::after 漸層） */
@keyframes shimmer {
  0%  { transform: translateX(-100%) }
  60% { transform: translateX(100%) }
  to  { transform: translateX(100%) }
}
/* 文字掃光（「思考中」效果）：背景漸層 + bg-clip-text，2.25s infinite */
@keyframes shimmertext {
  0%  { background-position: 100% 0 }
  65% { background-position: 0 0 }
  to  { background-position: 0 0 }
}

/* 載入 */
@keyframes spin { to { transform: rotate(1turn) } }
@keyframes pulse { 50% { opacity: .5 } }
@keyframes pulse-dot { 0%, to { transform: scale(.75) } 50% { transform: scale(1) } }
@keyframes ping { 75%, to { transform: scale(2); opacity: 0 } }
@keyframes loading-background {   /* 漸層背景四角輪轉 */
  0%, to { background-position: 0 0 } 25% { background-position: 100% 0 }
  50% { background-position: 100% 100% } 75% { background-position: 0 100% }
}

/* Accordion（Radix；高度用 --radix-accordion-content-height） */
@keyframes accordion-open  { 0% { height: 0; opacity: 0 } to { height: var(--radix-accordion-content-height); opacity: 1 } }
@keyframes accordion-close { 0% { height: var(--radix-accordion-content-height); opacity: 1 } to { height: 0; opacity: 0 } }
@keyframes slide-down { 0% { height: 0 } to { height: var(--radix-accordion-content-height) } }  /* 300ms ease-out */
@keyframes slide-up   { 0% { height: var(--radix-accordion-content-height) } to { height: 0 } }

/* Navbar 簾幕下拉（clip-path 揭露） */
@keyframes curtainDown { 0% { clip-path: inset(0 0 100% 0); opacity: 0 } to { clip-path: inset(0 0 0 0); opacity: 1 } }
@keyframes curtainUp   { 0% { clip-path: inset(0 0 0 0); opacity: 1 } to { clip-path: inset(0 0 100% 0); opacity: 0 } }

/* Toast（向右滑出；入場為 reverse 播放） */
@keyframes toast-slide { to { transform: translateX(100%) } }
```

### 6.35 ⚡ 2026-06 新增：`cds-*` 動畫命名空間（live 抓取）

```css
@keyframes cds-shimmer { 0% { transform: translate(-100%) } 100% { transform: translate(100%) } }
@keyframes cds-shimmer-text-shine { 0% { background-position: 150% 0 } 100% { background-position: -50% 0 } }
@keyframes cds-reveal-in { 0% { opacity: 0; transform: translateY(4px) } 100% { opacity: 1; transform: none } }
@keyframes cds-dot-pulse {                      /* 打字指示點 */
  0%, 5%   { background: var(--cds-alpha-3); animation-timing-function: cubic-bezier(.165,.84,.44,1) }
  15%, 25% { background: var(--cds-alpha-7); animation-timing-function: cubic-bezier(.165,.84,.44,1) }
  65%, 100%{ background: var(--cds-alpha-3) }
}
@keyframes check-spin-in {                      /* 完成勾勾旋入 */
  0% { transform: translateY(8px) rotate(-270deg) scale(0) }
  100% { transform: translateY(0) rotate(0) scale(1) }
}
```

舊 keyframes（fade/zoom/shimmer/shimmertext/blink…）全數仍在使用；另有 `look-around`、`voice-pulse`（語音 UI）等。

### 6.4 互動微動效（簽名手感）

```css
/* 按鈕按壓：縮小 1.5% */
.btn:active { transform: scale(0.985); }          /* active:scale-[0.985] */

/* Hero CTA hover：非等比放大 + 底部radial光暈浮現 */
.cta { transition: transform 150ms cubic-bezier(.165,.85,.45,1); will-change: transform; }
.cta:hover { transform: scaleY(1.015) scaleX(1.005); }
.cta::after {
  content: ""; position: absolute; inset: 0; opacity: 0; transform: translateY(8px);
  background: radial-gradient(at bottom, hsl(var(--bg-000) / 20%), hsl(var(--bg-000) / 0%));
  transition: opacity 200ms, transform 200ms;
}
.cta:hover::after { opacity: 1; transform: translateY(0); }
```

無障礙：所有動畫皆配 `motion-reduce:duration-0` 或 `motion-reduce:animate-[spin_1.5s_linear_infinite]`（減速版）。

### 6.5 claude.com motion stack

官網主站的動效不是只靠 CSS keyframes。`claude-brand.shared.9ce205edd.min.css` 內可直接解析到的 `@keyframes` 主要是 `spin`；其餘頁面動效主要由 Webflow interactions 與外部 JS 套件驅動。

| 層級 | 主要訊號 | 用途 |
|---|---|---|
| CSS keyframe | `spin` | spinner / loading |
| Carousel | Swiper 11 CSS/JS 出現在 `1,520` 個 canonical 頁 | logo wall、customer/resource carousel、橫向列表 |
| Scroll / reveal | GSAP `3.15.0` + `ScrollTrigger` + `SplitText` + `TextPlugin` + `Flip` + `Draggable` + `InertiaPlugin` | hero reveal、split text、拖曳/慣性、scroll-linked motion |
| Lottie | `dotlottie-player` + `lottie-web 5.12.2` | product/feature illustration motion |
| Webflow attrs | `data-animate-*`、`data-accordion`、`data-prompt-trigger`、`data-prompt-menu`、`data-lenis-prevent` | 卡片 reveal、accordion、prompt menu、smooth scroll guard |
| Button transition | `100–300ms`，hover 常以 border/spacer 寬度變化呈現 | 官網 CTA 的「邊框加倍」手感 |

結論：第 6 章的 App keyframes 仍保留，但官網應以「CSS token + Webflow/GSAP interaction layer」理解，不應把 App 的 `fade/zoom/shimmer` 直接當作所有 claude.com 頁面的來源。
