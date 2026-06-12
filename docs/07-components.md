# 07. 元件規格

> 所屬文件：[INDEX](./INDEX.md)

---

### 7.0 ⚡ 2026-06 live 實測更新

| 元件 | 實測規格 |
|---|---|
| **Composer（聊天輸入卡）** | `border-radius: 20px`、`bg-000` 白底、`1px` 邊框、陰影；內含 ＋選單、模型選擇 chip（`Opus 4.8 Max ⌄`）、麥克風與語音模式鈕 |
| **使用者訊息泡泡** | `border-radius: 12px`、底色 `#efeeeb`（= 新 `bg-300` / `_gray-50`）、內距 `10px 16px`、Anthropic Sans（`.font-large !font-user-message`） |
| **Claude 回覆** | Anthropic Serif `16px/24px` w400、容器 `.font-claude-response leading-[1.65rem]`、無泡泡直排；訊息操作列：Retry / Edit / Copy |
| **回覆內 pre 區塊** | `bg-bg-000/50` + `border-0.5 border-border-400` |
| **新對話問候** | 星芒（橘）+ serif 大字 `✳ Welcome, <名字>`，置中 |
| **風格 chips** | Code / Write / Learn / Life stuff / Claude's choice — pill 形、icon + 文字 |
| **促銷卡（右下）** | 白卡、插圖 + 標題 + 黑色 primary 按鈕 + 右上 X |


### 7.1 Button

**基底**（所有變體共用）：

```
inline-flex items-center justify-center relative shrink-0 select-none
border-[0.5px] overflow-hidden transition duration-100 backface-hidden
font-base-bold（w500 / dark w460）
whitespace-nowrap active:scale-[0.985]
disabled:pointer-events-none disabled:opacity-50 disabled:shadow-none
```

**尺寸**：

| 尺寸 | 高 | 內距 | 圓角 | 最小寬 | 字級 |
|---|---|---|---|---|---|
| 中（標準） | `h-9`（36px） | `px-4 py-2` | `rounded-lg`（8px） | `min-w-[5rem]` | `text-sm` |
| 大（表單/CTA） | `h-11`（44px） | `px-5` | `rounded-[0.6rem]` | `min-w-[6rem]` | `!text-base` |
| 小 | `h-[28px] min-w-7` | `px-3` | `rounded-lg` | — | `text-sm` |

**變體**（CSS 變數定義，1:1 取自 production）：

```css
/* Primary — 黑底白字（暗模式自動反轉為白底黑字，因 token 對調） */
.btn-primary {
  --button-bg: hsl(var(--text-000));        --button-text: hsl(var(--bg-000));
  --button-border: transparent;
  --button-hover-bg: hsl(var(--text-000));  --button-hover-text: hsl(var(--bg-000));
  --button-focus-outline: hsl(var(--text-000) / 75%);
}
/* Secondary — 透明底 + 髮絲框；hover 實底 */
.btn-secondary {
  --button-bg: transparent;                 --button-text: hsl(var(--text-000));
  --button-border: hsl(var(--border-200) / 30%);
  --button-hover-bg: hsl(var(--bg-400));    --button-hover-text: hsl(var(--text-000));
  --button-hover-border: transparent;
}
/* Ghost — 全透明；hover 淺底 */
.btn-ghost {
  --button-bg: transparent;                 --button-text: hsl(var(--text-300));
  --button-border: transparent;
  --button-hover-bg: hsl(var(--bg-300));    --button-hover-text: hsl(var(--text-100));
}
.btn-ghost:active, .btn-ghost[aria-expanded=true] { background: hsl(var(--bg-300)); }
/* Danger */
.btn-danger {
  --button-bg: hsl(var(--danger-200));      --button-text: hsl(var(--oncolor-100));
  --button-focus-outline: hsl(var(--danger-100) / 50%);
}
/* Claude — 品牌橘 */
.btn-claude {
  --button-bg: hsl(var(--accent-main-000)); --button-text: hsl(var(--oncolor-100));
  --button-hover-bg: hsl(var(--accent-main-200));
  --button-focus-outline: hsl(var(--accent-main-100) / 75%);
}
```

另有 `[data-color-context=danger]` 情境化機制：容器標記後，內部 secondary/ghost 按鈕自動轉紅色系（文字 `danger-000`、hover 底 `danger-100/8~10%`）。

### 7.2 Input（文字輸入框）

```
bg-bg-000 border border-border-300 hover:border-border-200
h-11 px-3 rounded-[0.6rem] w-full
font-large（w430） placeholder:text-text-500
transition-colors
disabled:cursor-not-allowed disabled:opacity-50
```

### 7.3 Focus Ring（全域）

```css
/* 平時透明 outline 佔位（避免跳動），focus-visible 才顯色 */
.can-focus, a, button, input, textarea {
  outline: 2px solid hsl(var(--accent-secondary-000) / 0%);
  outline-offset: 2px;
}
:is(.can-focus:focus-visible, a:focus-visible, button:focus-visible, input:focus-visible, textarea:focus-visible) {
  outline: 2px solid hsl(var(--accent-secondary-000) / 70%);  /* 藍色 ring */
  transition: outline .2s ease !important;
}
```

### 7.4 Card（卡片 — pricing / 內容卡）

```
flex flex-col rounded-3xl py-8 px-5 md:px-8 w-full
bg-bg-000 dark:bg-bg-200
border-[0.5px] border-border-400        ← 髮絲外框
transition-all duration-200 hover:shadow-sm
```

強調卡（如 Pro 方案）：

```
border-accent-secondary-000/20
shadow-diffused shadow-[hsl(var(--accent-secondary-000)/...)]   ← 藍色擴散光暈 0 4px 24px
```

### 7.5 Toast / 通知

- 位置：`fixed right-2 bottom-2 z-toast max-w-md`
- 外觀：`rounded-3xl bg-bg-500 p-4 sm:p-8 font-ui`
- 動畫：入場 `translateX 200ms cubic-bezier(.16,1,.3,1)`（reverse），離場 `fade 200ms ease-in`（reverse）；支援滑動關閉（Radix Toast，`--radix-toast-swipe-move-x`）

### 7.6 Navbar（行銷頁導覽列）

- 容器：`fixed top-0 inset-x-0 w-full bg-bg-100 dark:bg-bg-300 z-header`
- 下拉面板：`curtainDown 600ms cubic-bezier(.22,1,.36,1)`、`transform-origin: top center`、無邊框；關閉播 `curtainUp`
- 下拉內連結 hover：`background: hsl(var(--bg-000))`

### 7.7 Accordion（FAQ）

- Radix Accordion；展開 `slide-down 300ms ease-out`、收合 `slide-up 300ms ease-out`
- 標題：`font-claude-response`（襯線）`text-lg sm:text-xl md:text-2xl`
- 關閉態文字 `text-text-400`，hover `text-text-200`，開啟態 `text-text-000`，`transition-colors`

### 7.8 Spinner（載入）

```
h-20 w-20 rounded-full border-8 border-border-200 border-r-transparent
animate-spin（1s linear；motion-reduce 改 1.5s）
role="status" + <span class="sr-only">Loading...</span>
```

### 7.9 Modal / Overlay

- 遮罩：`bg-black/80`（重）或 `hsl(var(--always-black)/50%)`（中）＋ 常配 `backdrop-blur`
- 面板入場：`fade + zoom`（scale .95→1）約 200ms
- z-index：遮罩 50、面板 40~50

### 7.10 Scrollbar

```css
.custom-scroll::-webkit-scrollbar-thumb {
  background: hsl(var(--text-500) / .8);
  border-radius: 1em;
  border: 0.25rem solid transparent;   /* 製造軌道留白 */
}
.custom-scroll::-webkit-scrollbar-thumb:hover { background: hsl(var(--text-500)); }
.u-hidden-scrollbar { scrollbar-width: none; }
.u-hidden-scrollbar::-webkit-scrollbar { width: 0; opacity: 0; }
```

### 7.11 文字選取

```css
::selection {
  background-color: hsl(var(--accent-secondary-100));  /* 藍 #2c84db */
  color: hsl(var(--oncolor-100)) !important;
}
```

### 7.12 Toggle Group（分段切換，如 Individual / Team）

```
flex items-center justify-center h-[36px] px-6 gap-1.5 rounded-lg text-sm
text-text-500 hover:text-text-300 data-[state=on]:text-text-100
transition-colors duration-[250ms] motion-reduce:duration-0
```

### 7.13 官網按鈕（claude.com，4 主題）

| 主題 | 底 | 字 | hover |
|---|---|---|---|
| `brand` | `clay-interactive #c96442` | `gray-050` | **邊框寬度加倍**（官網簽名手感） |
| `primary` | `gray-950` | `gray-050` | 底 `gray-850` |
| `secondary` | `gray-200` | `gray-650` | 底變白、字變深 |
| `tertiary` | `gray-050` | `gray-600` | 字變深 |

nav 下拉：`300ms` 關 / `600ms` 開、`--ease-expo-out: cubic-bezier(0.16,1,0.3,1)`。實作類別 `.mkt-btn--*` 見 `tokens-marketing.css`。
