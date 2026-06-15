# Claude Design System 完整規格文件

> 範圍：claude.ai App 的 token、shell、composer、設定與功能頁；claude.com marketing/docs 的色彩、排印、元件、頁型與品牌資產；production CSS 存檔作為穩定性對照。
> 文件版本：**v3.2 — 2026-06 雙站整併 + claude.com 頁型與元件矩陣 + claude.ai App shell/composer/設定 + 新 UI 設計推導指南**（primitive 色板層 + 語意層）；console 主題沿用 2026-01 存檔。
> 用途：依本文件即可重建 claude.ai App 與 claude.com 官網的主要視覺、元件、頁型規格，並推導未列出的 Claude-style UI。

---

## 目錄

1. [主題架構](#1-主題架構)
2. [色彩系統](#2-色彩系統)
3. [字體排印](#3-字體排印)
4. [佈局：斷點、z-index、間距](#4-佈局斷點z-index間距)
5. [圓角、邊框、陰影](#5-圓角邊框陰影)
6. [動效系統](#6-動效系統)
7. [元件規格](#7-元件規格)
8. [圖示與品牌](#8-圖示與品牌)
9. [內容樣式：程式碼、Markdown、數學式](#9-內容樣式程式碼markdown數學式)
10. [頁面結構模式](#10-頁面結構模式)
11. [實作指南](#11-實作指南)
12. [新 UI 設計推導指南](#12-新-ui-設計推導指南)

---

## 1. 主題架構

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

## 2. 色彩系統

### 2.0 ⚡ 2026-06 App 色彩架構：Primitive 色板層

claude.ai 已將色彩系統重構為**兩層架構**，並改了部分命名：

```
Primitive 層（--_*，317 個）        語意層（45 個 / 模式）
--_gray-0 … --_gray-900     ←──    --bg-000: var(--_gray-20)
--_blue-0 … --_blue-900     ←──    --accent-000: var(--_blue-600)
--_brand-clay               ←──    --accent-brand: var(--_brand-clay)
（10 條色階：gray/red/orange/yellow/green/aqua/blue/violet/magenta + brand-clay）
（每條 0,10–100,150–900；800–890 為暗模式微調細階）
```

**命名異動**：

| 2026-01 舊名 | 2026-06 新名 |
|---|---|
| `--accent-main-*`（橘） | **`--brand-*`** |
| `--accent-secondary-*`（藍） | **`--accent-*`** |
| 字體 `anthropicSans` / `anthropicSerif` / `jetbrains` | **`"Anthropic Sans"` / `"Anthropic Serif"` / `"Anthropic Mono"`** |

**語意層映射（2026-06）**：

| Token | Light（引用 / HSL / HEX） | Dark |
|---|---|---|
| `bg-000` | `--_gray-0` | `0 0% 100%` | `#ffffff` | `--_gray-750` | `60 2% 17%` | `#2c2c2a` |
| `bg-100`（頁面底） | `--_gray-20` | `60 14% 97%` | `#f8f8f6` | `--_gray-800` | `60 2% 12%` | `#1f1f1e` |
| `bg-200` | `--_gray-40` | `60 11% 95%` | `#f4f4f1` | `--_gray-840` | `60 2% 9%` | `#171716` |
| `bg-300` | `--_gray-50` | `45 12% 93%` | `#efeeeb` | `--_gray-860` | `0 0% 7%` | `#121212` |
| `bg-400` | `--_gray-80` | `50 11% 89%` | `#e6e5e0` | `--_gray-900` | `0 0% 4%` | `#0a0a0a` |
| `text-000` | `--_gray-860` | `0 0% 7%` | `#121212` | `--_gray-20` | `60 14% 97%` | `#f8f8f6` |
| `text-200` | `--_gray-700` | `60 3% 21%` | `#373734` | `--_gray-200` | `55 9% 74%` | `#c3c2b7` |
| `text-400` | `--_gray-450` | `43 3% 47%` | `#7b7974` | `--_gray-350` | `48 5% 57%` | `#97958c` |
| `border-*`（四階同值） | `--_gray-810` | `60 2% 12%` | `#1f1f1e` | `--_gray-100` | `53 12% 87%` | `#e2e1da` |
| `accent-brand` | `--_brand-clay` | `14.8 63.1% 59.6%` | `#d97757` | `--_brand-clay` | `14.8 63.1% 59.6%` | `#d97757` |
| `brand-000` | `--_brand-clay-emphasized` | `15.1 54.2% 51.2%` | `#c6613f` | `--_brand-clay-emphasized` | `15.1 54.2% 51.2%` | `#c6613f` |
| `brand-200` | `--_brand-clay` | `14.8 63.1% 59.6%` | `#d97757` | `--_brand-clay` | `14.8 63.1% 59.6%` | `#d97757` |
| `accent-000`（藍） | `--_blue-600` | `214 72% 34%` | `#184e95` | `--_blue-200` | `213 80% 79%` | `#9fc5f4` |
| `accent-100` | `--_blue-500` | `213 68% 45%` | `#256bc1` | `--_blue-350` | `212 75% 62%` | `#5599e7` |
| `accent-pro-000`（紫） | `--_violet-600` | `249 48% 44%` | `#4b3aa6` | `--_violet-200` | `246 75% 84%` | `#beb8f5` |
| `danger-100` | `--_red-450` | `0 61% 52%` | `#cf3a3a` | `--_red-400` | `0 73% 59%` | `#e34a4a` |
| `success-100` | `--_green-450` | `82 100% 27%` | `#578a00` | `--_green-400` | `81 100% 30%` | `#639900` |
| `warning-100` | `--_yellow-450` | `38 100% 33%` | `#a86b00` | `--_yellow-400` | `39 100% 36%` | `#b87700` |

> 注意視覺走向：亮模式底色由米白 `#faf9f5` 微調為更中性的 `#f8f8f6`；**暗模式整體加深**（頁面底 `#262624`→`#1f1f1e`、最深階到 `#0a0a0a`）；藍色 accent 加深（`#1b67b2`→`#184e95`）；success 由純綠改**黃綠**（`#578a00`）。完整 317 個 primitive 與全部語意映射見 `tokens.css` / `tokens.json`。

**官方具名品牌色（`--cds-*` 命名空間）**：

| 名稱 | HEX | 名稱 | HEX |
|---|---|---|---|
| `clay`（品牌橘） | `#d97757` | `cactus` | `#bcd1ca` |
| `clay-emphasized` | `#c6613f` | `mineral` | `#629987` |
| `heather` | `#cbcadb` | `peach` | `#ebc9b7` |
| `plum` | `#827dbd` | | |

`tokens.css` 已含完整 primitive 層 + 新語意層 + **舊名相容別名**（`--accent-main-*`→`--brand-*` 等），舊寫法可無痛沿用。

以下 2.1–2.5 為 **2026-01 版數值**，保留作歷史對照（行銷頁仍大致適用）。

### 2.1 全域不變色（2026-01 存檔）

| Token | HSL | HEX | 用途 |
|---|---|---|---|
| `--always-white` | `0 0% 100%` | `#ffffff` | 不隨主題反轉的白（疊加、遮罩） |
| `--always-black` | `0 0% 0%` | `#000000` | 不隨主題反轉的黑（陰影、遮罩） |

### 2.2 Claude 主題 — Light Mode（`[data-theme=claude]`）

#### 背景階層（bg）— 招牌米白暖灰階

| Token | HSL | HEX | 用途 |
|---|---|---|---|
| `--bg-000` | `0 0% 100%` | `#ffffff` | 卡片、輸入框、最上層表面 |
| `--bg-100` | `48 33.3% 97.1%` | `#faf9f5` | **頁面主背景（招牌米白）** |
| `--bg-200` | `53 28.6% 94.5%` | `#f5f4ed` | 次層背景、側欄、hover |
| `--bg-300` | `48 25% 92.2%` | `#f0eee6` | 區塊底、按下狀態、占位圖底 |
| `--bg-400` | `50 20.7% 88.6%` | `#e8e6dc` | 更深層、secondary hover |
| `--bg-500` | `50 20.7% 88.6%` | `#e8e6dc` | 最深層（cookie toast 底） |

#### 文字階層（text）

| Token | HSL | HEX | 用途 |
|---|---|---|---|
| `--text-000` | `60 2.6% 7.6%` | `#141413` | 最高對比（標題、primary 按鈕底） |
| `--text-100` | `60 2.6% 7.6%` | `#141413` | 內文主色 |
| `--text-200` | `60 2.5% 23.3%` | `#3d3d3a` | 次要文字 |
| `--text-300` | `60 2.5% 23.3%` | `#3d3d3a` | 次要文字（與 200 同值） |
| `--text-400` | `51 3.1% 43.7%` | `#73726c` | 弱化文字、說明 |
| `--text-500` | `51 3.1% 43.7%` | `#73726c` | Placeholder、最弱文字 |

#### 邊框（border）— 單一深色 + 透明度

| Token | HSL | HEX | 備註 |
|---|---|---|---|
| `--border-100` ~ `--border-400` | `30 3.3% 11.8%` | `#1f1e1d` | 四階同值；**實際視覺由透明度決定**，慣用 `/ 30%`、`/ 15%`、`/ 20%` |

#### 主強調色（accent-main）— Claude 橘

| Token | HSL | HEX | 用途 |
|---|---|---|---|
| `--accent-brand` | `15 63.1% 59.6%` | `#d97757` | **品牌橘（logo、品牌元素）** |
| `--accent-main-000` | `15 54.2% 51.2%` | `#c6613f` | 橘色按鈕底色 |
| `--accent-main-100` | `15 54.2% 51.2%` | `#c6613f` | focus ring 用 |
| `--accent-main-200` | `15 63.1% 59.6%` | `#d97757` | 橘色按鈕 hover |
| `--accent-main-900` | `0 0% 0%` | `#000000` | 橘色系最深（特殊陰影） |

#### 次強調色（accent-secondary）— 藍（連結、focus、選取）

| Token | HSL | HEX | 用途 |
|---|---|---|---|
| `--accent-secondary-000` | `210 73.7% 40.2%` | `#1b67b2` | 連結、focus ring |
| `--accent-secondary-100` | `210 70.9% 51.6%` | `#2c84db` | **文字選取背景**、ring |
| `--accent-secondary-200` | `210 70.9% 51.6%` | `#2c84db` | 同 100 |
| `--accent-secondary-900` | `211 72% 90%` | `#d3e5f8` | 淡藍底 |

#### Pro / 進階方案紫（accent-pro）

| Token | HSL | HEX |
|---|---|---|
| `--accent-pro-000` | `251 34.2% 33.3%` | `#433872` |
| `--accent-pro-100` | `251 40% 45.1%` | `#5645a1` |
| `--accent-pro-200` | `251 61% 72.2%` | `#9d8de3` |
| `--accent-pro-900` | `253 33.3% 91.8%` | `#e6e3f1` |

#### 狀態色（danger / success / warning）

| Token | HSL | HEX |
|---|---|---|
| `--danger-000` | `0 58.6% 34.1%` | `#8a2424` |
| `--danger-100` / `-200` | `0 56.2% 45.4%` | `#b53333` |
| `--danger-900` | `0 50% 95%` | `#f9ecec` |
| `--success-000` | `125 100% 18%` | `#005c08` |
| `--success-100` / `-200` | `103 72.3% 26.9%` | `#2f7613` |
| `--success-900` | `86 45.1% 90%` | `#e7f1da` |
| `--warning-000` | `45 91.8% 19%` | `#5d4704` |
| `--warning-100` / `-200` | `39 88.8% 28%` | `#875a08` |
| `--warning-900` | `38 65.9% 92%` | `#f8eedd` |

> 命名規律：`000` 最深（文字級）、`100/200` 標準（按鈕/icon 級）、`900` 最淡（背景級）。深色模式下方向反轉（`000` 變最亮）。

#### 彩色疊文字色（oncolor）與插圖色（pictogram）

| Token | HSL | HEX | 用途 |
|---|---|---|---|
| `--oncolor-100` | `0 0% 100%` | `#ffffff` | 彩色底上的文字 |
| `--oncolor-200` / `-300` | `60 6.7% 97.1%` | `#f8f8f7` | 同上（暖白） |
| `--pictogram-100` | `50 20.7% 88.6%` | `#e8e6dc` | 插圖層次 1 |
| `--pictogram-200` | `51 16.5% 84.5%` | `#dedcd1` | 插圖層次 2 |
| `--pictogram-300` | `0 0% 100%` | `#ffffff` | 插圖層次 3 |
| `--pictogram-400` | `48 33.3% 97.1%` | `#faf9f5` | 插圖層次 4 |

### 2.3 Claude 主題 — Dark Mode（`[data-theme=claude][data-mode=dark]`）

| Token | HSL | HEX | 備註 |
|---|---|---|---|
| `--bg-000` | `60 2.1% 18.4%` | `#30302e` | 卡片表面（暗模式中最亮） |
| `--bg-100` | `60 2.7% 14.5%` | `#262624` | **頁面主背景** |
| `--bg-200` | `30 3.3% 11.8%` | `#1f1e1d` | 側欄、次層 |
| `--bg-300` | `60 2.6% 7.6%` | `#141413` | 更深 |
| `--bg-400` / `-500` | `0 0% 0%` | `#000000` | 最深 |
| `--text-000` / `-100` | `48 33.3% 97.1%` | `#faf9f5` | 主文字（= 亮模式背景色！） |
| `--text-200` / `-300` | `50 9% 73.7%` | `#c2c0b6` | 次要文字 |
| `--text-400` / `-500` | `48 4.8% 59.2%` | `#9c9a92` | 弱化文字 |
| `--border-100~400` | `51 16.5% 84.5%` | `#dedcd1` | 亮色 + 低透明度使用 |
| `--accent-brand` | `15 63.1% 59.6%` | `#d97757` | 品牌橘不變 |
| `--accent-main-000` | `15 54.2% 51.2%` | `#c6613f` | |
| `--accent-main-100` / `-200` | `15 63.1% 59.6%` | `#d97757` | hover 用較亮橘 |
| `--accent-pro-000` | `251 84.6% 74.5%` | `#9b87f5` | 暗模式紫變亮 |
| `--accent-pro-100` | `251 40.2% 54.1%` | `#6c5bb9` | |
| `--accent-pro-200` | `251 40% 45.1%` | `#5645a1` | |
| `--accent-pro-900` | `250 25.3% 19.4%` | `#29253e` | |
| `--accent-secondary-000` | `210 65.5% 67.1%` | `#74abe2` | 連結變亮藍 |
| `--accent-secondary-100` / `-200` | `210 70.9% 51.6%` | `#2c84db` | |
| `--accent-secondary-900` | `210 55.9% 24.6%` | `#1c3f62` | |
| `--danger-000` | `0 98.4% 75.1%` | `#fe8181` | |
| `--danger-100` / `-200` | `0 67% 59.6%` | `#dd5353` | |
| `--danger-900` | `0 46.5% 27.8%` | `#682626` | |
| `--success-000` | `97 59.1% 46.1%` | `#65bb30` | |
| `--success-100` / `-200` | `97 75% 32.9%` | `#459315` | |
| `--success-900` | `127 100% 13.9%` | `#004708` | |
| `--warning-000` | `40 71% 50%` | `#da9e25` | |
| `--warning-100` / `-200` | `39 93.4% 35.9%` | `#b17506` | |
| `--warning-900` | `45 94.8% 15.1%` | `#4b3902` | |
| `--pictogram-100` | `48 3.4% 29.2%` | `#4d4c48` | |
| `--pictogram-200` | `60 2.5% 23.3%` | `#3d3d3a` | |
| `--pictogram-300` | `60 2.1% 18.4%` | `#30302e` | |
| `--pictogram-400` | `60 2.7% 14.5%` | `#262624` | |
| `--oncolor-*` | 同亮模式 | | |

`data-mode="auto"` 時，相同的 dark 變數組包在 `@media (prefers-color-scheme: dark)` 內套用。

### 2.4 Console 主題（開發者後台，永久深色）

純中性灰階（無暖色調）：`--bg-000: 0 0% 6%`、`--bg-100: 0 0% 10%`、`--bg-200: 0 0% 14%`、`--bg-300: 0 0% 17%`、`--bg-400: 0 0% 19%`、`--bg-500: 0 0% 23%`；`--text-000: 0 0% 100%` → `--text-500: 45 0.6% 48%` 六階；accent-secondary 改用綠色系（`158 68% 32%` 等）。完整數值見 `tokens.json`。

### 2.5 慣用透明度組合

| 場景 | 寫法 |
|---|---|
| 標準邊框 | `hsl(var(--border-200) / 30%)` |
| 細邊框（卡片） | `hsl(var(--border-300) / 15%)` |
| 陰影 | `hsl(var(--always-black) / 4~8%)`（亮）、`/ 24%`（暗） |
| Modal 遮罩 | `hsl(var(--always-black) / 50%)` 或 `rgb(0 0 0 / 0.8)` |
| 暗模式微光底 | `hsl(var(--always-white) / 6%)`、邊框 `/ 9%` |
| Disabled | `opacity: 0.5` |

### 2.7 官網色彩：Swatch 色板 + 8 種區段主題（claude.com）

**灰階**（暖灰 21 階；`gray-050 = #faf9f5` 經典米白在官網保留）：
`000 #fff → 050 #faf9f5 → 100 #f5f4ed → 150 #f0eee6 → 200 #e8e6dc → 250 #dedcd1 → 300 #d1cfc5 → 350 #c2c0b6 → 400 #b0aea5 → 450 #9c9a92 → 500 #87867f → 550 #73726c → 600 #5e5d59 → 650 #4d4c48 → 700 #3d3d3a → 750 #30302e → 800 #262624 → 850 #1f1e1d → 900 #1a1918 → 950 #141413 → 1000 #000`

**品牌色（官方 swatch 命名）**：

| 名稱 | HEX | 名稱 | HEX |
|---|---|---|---|
| `clay`（品牌橘） | `#d97757` | `heather` | `#cbcadb` |
| `clay-interactive`（按鈕） | `#c96442` | `plum` | `#827dbd` |
| `oat` | `#e3dacc` | `sky` | `#6a9bcc` |
| `peach` | `#ebc9b7` | `cactus` | `#bcd1ca` |
| `coral` | `#ebcece` | `mineral` | `#629987` |
| `fig` | `#c46686` | `olive` | `#788c5d` |

**8 種區段主題**（section 掛 `.u-theme-*` 整段換膚）：`light`/`ivory`（米白＋clay accent）、`white`、`neutral-1`（#f0eee6）、`neutral-2`（#e8e6dc）、`dark-1`（#262624）、`dark-2`（#1f1e1d）、`dark-3`（#141413，accent `#c46849`）。

**::selection 為 clay 50% 透明**（`color-mix(in srgb, var(--swatch--clay) 50%, transparent)`）— 與 App 的藍底不同。實作見 `tokens-marketing.css`。

### 2.8 claude.com 色彩規格補充

claude.com 的 `34` 個 `--swatch--*` 色票與 §2.7 一致；官網實作時還需要保留以下語意用色：

| 用途 | Token / 值 | 備註 |
|---|---|---|
| 主要背景 | `--_theme---background-primary: var(--swatch--gray-050)` | `#faf9f5`，官網仍保留經典米白 |
| 次層/第三層背景 | `gray-100` / `gray-150` | 卡片、選單、分段選擇底色 |
| 主要文字 | `--_theme---foreground-primary: gray-950` | `#141413` |
| 次要/弱化文字 | `gray-750` / `gray-600` | route cards、caption、metadata |
| Brand CTA | `clay-interactive #c96442` | hover 不改底色，主要用邊框與 spacer 變化 |
| Switch active | `#2c84db` | 官網 toggle/switch 的 active 藍 |
| Error text | `#b53333` | 表單錯誤文字 |
| Selection | `color-mix(... clay 50%, transparent)` | 官網文字選取不是 App 的藍底白字 |

結論：Claude 的色彩系統是雙軌：App 以 HSL semantic token 為主，官網以 swatch 與 `.u-theme-*` 區段主題為主。

## 3. 字體排印

### 3.0 ⚡ 2026-06 現行排印

- 字體正名：**`"Anthropic Sans"` / `"Anthropic Serif"` / `"Anthropic Mono"`**（等寬字不再是 JetBrains Mono！）
- serif 後備鏈加入完整 CJK 字串：`Georgia, "Arial Hebrew", …, "Hiragino Sans", "Yu Gothic", "PingFang TC", "Microsoft JhengHei", "PingFang SC", "Microsoft YaHei", "Apple SD Gothic Neo", serif`
- App body 使用 16px Anthropic Sans；Claude 回覆為 **Anthropic Serif 16px / 24px / w400**（`.font-claude-response leading-[1.65rem]`）；使用者訊息為 `.font-large !font-user-message`（sans）

### 3.1 字族（2026-01 存檔）

| 變數 | 字體 | 格式 | 後備 |
|---|---|---|---|
| `--font-anthropic-sans` | **anthropicSans**（自製可變字體，wght 300–800） | woff2 | `system-ui, "Segoe UI", Roboto, Helvetica, Arial, sans-serif` |
| `--font-anthropic-serif` | **anthropicSerif**（自製可變字體，wght 300–800，支援 opsz） | woff2 | `Georgia, "Times New Roman", Times, serif` |
| `--font-jetbrains` | **JetBrains Mono**（w400） | woff2 | `ui-monospace, SFMono-Regular, Menlo, Monaco, monospace` |
| `--font-open-dyslexic` | OpenDyslexic（無障礙選項，w400/500） | woff2 | `"Comic Sans MS", ui-serif, Georgia, serif` |

兩款 Anthropic 字體皆設定 `font-feature-settings: "dlig" 0`（停用任意連字）、`font-display: swap`。

**後備字體度量補償**（next/font 自動產生，自行實作可沿用以減少 CLS）：

```css
@font-face { font-family: "anthropicSans Fallback"; src: local("Arial");
  ascent-override: 92.99%; descent-override: 24.13%; line-gap-override: 0%; size-adjust: 106.73%; }
@font-face { font-family: "anthropicSerif Fallback"; src: local("Arial");
  ascent-override: 93.60%; descent-override: 24.28%; line-gap-override: 0%; size-adjust: 106.03%; }
@font-face { font-family: "jetbrains Fallback"; src: local("Arial");
  ascent-override: 77.57%; descent-override: 22.82%; line-gap-override: 0%; size-adjust: 131.49%; }
```

> 授權注意：anthropicSans / anthropicSerif 為 Anthropic 私有字體，不可直接取用。視覺最接近的替代：sans 可用 **Styrene B**（商用）或 Inter / "Söhne"；serif 可用 **Tiempos Text** 或 Source Serif 4（開源、有 opsz 軸）。

### 3.2 語意字族變數

```css
:root {
  --font-ui:              var(--font-anthropic-sans);   /* 介面預設 */
  --font-ui-serif:        var(--font-anthropic-serif);  /* 標題襯線 */
  --font-claude-response: var(--font-anthropic-serif);  /* Claude 回覆內文（襯線！） */
  --font-user-message:    var(--font-ui);               /* 使用者訊息（無襯線） */
  --font-mono:            var(--font-anthropic-mono);   /* 2026-06 起 */
  --font-sans-serif:      var(--font-ui);
  --font-serif:           var(--font-ui-serif);
  --font-system:          system-ui, sans-serif;
}
body, html { font-family: var(--font-ui); }
```

> 歷史 note：2026-01 存檔仍可見 `--font-jetbrains` / JetBrains Mono；2026-06 現行 App 與官網皆以 **Anthropic Mono** 為等寬字。

> **核心視覺特徵**：Claude 的 AI 回覆使用「襯線體」、使用者訊息與 UI 使用「無襯線體」——這是 claude.ai 最易識別的排印決策。

### 3.3 字重系統（含暗模式補償）

可變字體使用非整數百位字重；**暗模式整體調降字重**以補償亮字在深底的視覺增粗：

| 類別 | Light | Dark |
|---|---|---|
| `.font-normal` | 430 | 400 |
| `.font-medium` | 550 | 510 |
| `.font-semibold` | 580 | 540 |
| `.font-bold` / `b` / `strong` | 600 | 530 |
| `.font-base` | 430 | 400 |
| `.font-base-bold` | 500 | 460 |
| `.font-large` | 430 | 400 |
| `.font-large-bold` | 600 | 560 |
| `.font-small` | 430 | 400 |
| `.font-small-bold` | 600 | 560 |
| `.font-xl` | 400 | 360 |
| `.font-xl-bold` | 600 | 560 |
| `.font-display` | 330 | 290 |
| `.font-heading` | 500 | 460 |
| `.font-title` | 500 | 460 |
| `.font-claude-response`（內文） | 400 | 360 |
| `.font-claude-response-bold` | — | 530 |
| `.font-claude-response-heading/title/subheading` | 600 | 530 |

### 3.4 文字樣式（Type Styles）

| 類別 | 字族 | 大小/行高 | 字重 | 光學尺寸 |
|---|---|---|---|---|
| `.font-display` | serif | `2.375rem / 1.2` | 330 | `"opsz" 48` |
| `.font-title` | serif | `1.75rem / 1.3` | 500 | `"opsz" 28` |
| `.font-heading` | serif | `1.5rem / 1.3` | 500 | `"opsz" 24` |
| `.font-claude-response-title` | serif | `1.75rem / 1.3` | 600 | `"opsz" 28` |
| `.font-claude-response-heading` | serif | `1.25rem / 1.4` | 600 | `"opsz" 20` |
| `.font-claude-response-subheading` | serif | `1rem / 1.35` | 600 | — |
| `.font-claude-response-code` | mono | `0.813rem / 1.5` | 400 | — |

尺寸刻度（Tailwind 標準）：`xs .75/1rem`、`sm .875/1.25`、`base 1/1.5`、`lg 1.125/1.75`、`xl 1.25/1.75`、`2xl 1.5/2`、`3xl 1.875/2.25`、`4xl 2.25/2.5`、`5xl 3rem/1`。

Landing 主標（"Impossible?"）：`text-[1.75rem]` → `min-[350px]:text-[3.2rem]` → `min-[500px]:text-[3.5rem]`，serif `font-display`。

### 3.5 其他排印細節

- 全站 `-webkit-font-smoothing: antialiased`（html class `antialiased`）+ `scroll-smooth`
- 等寬字停用連字：`code, kbd, pre, samp { font-variant-ligatures: none; font-feature-settings: "calt" 0, "liga" 0; }`
- `letter-spacing` 幾乎不用，僅 `.tracking-wider (0.05em)` 用於小型大寫標籤

### 3.6 官網流式字級（claude.com）

字族同 App + `logographic: "Noto Sans"`（CJK 補字）。全部字級以 `clamp()` 在 320→1440px 線性內插：

| 級別 | 320px | 1440px | 級別 | 320px | 1440px |
|---|---|---|---|---|---|
| display-1 | 2.625rem | **4.5rem** | h4 | 1.4375rem | 2rem |
| display-2 | 2.25rem | 4rem | h5 | 1.25rem | 1.5625rem |
| h1 | 2.125rem | 3.25rem | h6 | 1rem | 1.1875rem |
| h2 | 1.875rem | 2.75rem | body-large-1 | 1.375rem | 1.5rem |
| h3 | 1.75rem | 2.25rem | body-large-2 | 1.25rem | 1.4375rem |
| body-1 | 1.1875rem | 1.25rem | body-2 | 1.0625rem | 1.0625rem |

固定級：body-3 `0.9375rem`、caption `0.75rem`、micro `0.625rem`。行高刻度 `1/1.1/1.2/1.3/1.5/1.6/1.7`；**text-trim 模擬**（上 `.39em` 下 `.38em`，mono 為 `.4em/.37em`）做首行精準對齊。

### 3.7 claude.com 排印規格補充

官網 typography token 與上表一致，另有幾個實作細節會影響跨頁一致性：

| 類型 | 最新值 | 設計含義 |
|---|---|---|
| Primary | `"Anthropic Sans", Arial, sans-serif` | 官網 UI、導覽、卡片、按鈕預設 |
| Secondary | `"Anthropic Serif", Georgia, sans-serif` | 官網大型標題、editorial headline；雖 fallback 字串最後是 `sans-serif`，實際第二順位仍是 Georgia |
| Mono | `"Anthropic Mono", Arial, sans-serif` | 程式碼與技術文件；App 端舊 JetBrains 說明只作歷史對照 |
| Logographic | `"Noto Sans", Arial, sans-serif` | CJK/多語系頁面補字，解釋多語頁沒有獨立版型但仍能承受字幅差異 |
| Letter spacing | `0em` / `.01em` / `.05em` | 多數文字不收字距；caption/eyebrow 才使用小幅 spacing 或 uppercase |

結論：官網排印以 Anthropic Sans / Serif / Mono 三字族、流式字級、text-trim 模擬與多語 fallback 鏈共同維持節奏。

## 4. 佈局：斷點、z-index、間距

### 4.1 斷點

| 名稱 | min-width | 備註 |
|---|---|---|
| `min-[350px]` | 350px | 自訂（小手機） |
| `min-[500px]` | 500px | 自訂 |
| `sm` | 640px | Tailwind 標準 |
| `md` | 768px | |
| `min-[1000px]` | 1000px | 自訂（landing 雙欄切換點） |
| `lg` | 1024px | |
| `xl` | 1280px | |
| `2xl` | 1536px | |

### 4.2 z-index 語意刻度

```css
--z-header:   20;   /* 固定導覽列 */
--z-sidebar:  30;   /* 側欄 */
--z-modal:    40;   /* 對話框 */
--z-dropdown: 50;   /* 下拉選單 */
--z-overlay:  50;   /* 遮罩 */
--z-tooltip:  50;   /* 提示框 */
--z-toast:    60;   /* 通知（最高） */
```

### 4.3 間距

採 Tailwind 標準 4px 刻度（`0.25rem` 基數）。常見模式：

- 卡片內距：`py-8 px-5 md:px-8`（32px / 20→32px）
- 按鈕：`px-4`（中）/ `px-5`（大）
- 輸入框：`px-3`
- 區塊間距：大量使用 `gap-2`（8px）、`gap-1.5`（6px）

### 4.4 官網佈局（claude.com）

- **Responsive grid**：shared CSS 預設 `--_grid---column-count: 6`、`--_grid---gutter: 1rem`；桌面/大型 section 常見 12 欄 pattern、gutter `2rem`
- **Site margin**：流式 `2→4rem`；容器 `main 90rem / medium 74.5rem / small 60rem / full 100%`
- **區段間距全流式**：small `4→6rem`、main `6→8rem`、large `8→12.5rem`、page-top `12→15rem`
- 斷點（Webflow）：`≤479` / `480–767` / `768–991` / `≥992`
- 邊框 `0.0625rem`；focus ring `0.125rem`；官網圓角另有流式 `x-large 1→1.5rem`、`xx-large 1→2rem`

### 4.5 claude.com 佈局 token

官網佈局 token 的完整組成：

| 類型 | Token / 值 | 用途 |
|---|---|---|
| viewport | `--site--viewport-min: 20`、`--site--viewport-max: 90` | `clamp()` 以 320px→1440px 內插 |
| container | `main 90rem`、`medium 74.5rem`、`small 60rem`、`full 100%` | hero、內容頁、docs/landing 依版型取用 |
| grid | 預設 `6` 欄 / `1rem` gutter | mobile-first；桌面 template 再擴成 12 欄 |
| section spacing | `none 0`、`small 4→6rem`、`main 6→8rem`、`large 8→12.5rem`、`page-top 12→15rem` | 官網區段垂直 rhythm |
| spacing scale | `.25/.5/.75/1/1.5rem` 固定，`2/2.5/3/4/5/6rem` 多為流式 | 卡片內距、logo wall、feature grid |

結論：官網版面採 mobile-first `6/1rem` grid，桌面大型 section 再擴成 `12/2rem`，不要只用單一桌面欄數推導所有頁面。

## 5. 圓角、邊框、陰影

### 5.1 圓角刻度

| 類別 | 值 | 用途 |
|---|---|---|
| `rounded` | `0.25rem` | 小元素 |
| `rounded-md` | `0.375rem` | |
| `rounded-lg` | `0.5rem` | **標準按鈕** |
| `rounded-[0.6rem]` | `0.6rem`（9.6px） | **大按鈕、輸入框（招牌值）** |
| `rounded-xl` | `0.75rem` | 圖片容器 |
| `rounded-2xl` | `1rem` | 預覽容器、聊天輸入框 |
| `rounded-3xl` | `1.5rem` | **卡片（pricing 等）、toast** |
| `rounded-full` | `9999px` | 圓形（頭像、spinner、pill） |
| 自訂雜項 | `6/10/12/14/20/24/30px`、`2rem` | 個別元件 |

### 5.2 邊框寬度

| 類別 | 值 | 備註 |
|---|---|---|
| `border-0.5` | **`0.5px`** | **招牌髮絲線**——卡片與按鈕外框幾乎都用這個 |
| `border` | `1px` | 輸入框、表格 |
| `border-1.5` | `1.5px` | |
| `border-2 / 4 / 8` | `2/4/8px` | 8px 用於大型 spinner |

### 5.3 陰影刻度（實際出現於 production 的值）

```css
/* 亮模式 — 陰影極淡（4~8% 黑） */
--shadow-sm:       0 1px 2px 0 hsl(var(--always-black) / 5%);
--shadow-md:       0 2px 8px 0 hsl(var(--always-black) / 8%);
--shadow-soft:     0 2px 12px hsl(var(--always-black) / 5%);
--shadow-card:     0 4px 20px 0 hsl(var(--always-black) / 4%);
--shadow-elevated: 0 6px 22px 0 hsl(var(--always-black) / 5%);
--shadow-diffused: 0 4px 24px var(--tw-shadow-color);  /* 搭配自訂色，如 accent-secondary 光暈 */
--shadow-pop:      0 25px 50px -12px rgb(0 0 0 / 0.25);
--shadow-huge:     0 40px 80px hsl(var(--always-black) / 10%);

/* 暗模式 — 透明度加重 */
--shadow-md-dark:  0px 2px 8px 0px hsl(var(--always-black) / 24%);
--shadow-lg-dark:  0px 4px 20px 0px hsl(var(--always-black) / 20%);
--shadow-xl-dark:  0px 8px 28px 0px hsl(var(--always-black) / 25%);

/* 特殊 */
--glow-accent: 0px 0px 32px 0px hsl(var(--accent-main-900) / 12%);   /* 橘色光暈 */
--inset-well:  inset 0 1px 4px 2px hsl(var(--always-black) / 12%);   /* 內凹 */
--hairline-b:  0px 1px 0px 0px hsl(var(--border-300) / 0.15);        /* 底部髮絲分隔 */

/* App 浮層 elevation */
--shadow-menu:     0 0 0 1px hsl(var(--border-100) / 10%),
                   0 8px 24px hsl(var(--always-black) / 12%),
                   0 2px 6px hsl(var(--always-black) / 8%);   /* 選單/下拉 popover；搭配 radius 12px、bg-000 */
--shadow-composer: 0 0 0 .5px hsl(var(--border-300) / 30%),
                   0 4px 20px hsl(var(--always-black) / 3.5%); /* 聊天輸入卡：髮絲 ring + 柔陰影取代實線 border；radius 20px */
```

> **App 浮層慣例**：「浮在內容之上」的層（menu / dropdown / composer / modal / toast）才用陰影，且用上面的 `--shadow-menu` / `--shadow-composer` 這類「1px/0.5px 邊界 ring + 一道柔和投影」組合——ring 維持邊界清晰、投影量極小（亮模式 3.5~12% 黑）。一般卡片與表面仍只用 `0.5px` 髮絲線 + 底色分層，不疊陰影。

毛玻璃：`backdrop-blur` 常用 `8px / 12px / 14px / 16px / 24px / 40px`。

### 5.4 官網形狀與焦點規格（claude.com）

官網有自己的 shape token，與 App 的 `0.5px` 髮絲線系統不同：

| 類型 | Token / 值 | 用途 |
|---|---|---|
| radius x-small | `.25rem` | 小 icon、tag、局部 media |
| radius small | `.5rem` | 小按鈕、chip、code pill |
| radius main | `.75rem` | 標準卡片、tabs active item |
| radius large | `1rem` | segmented selector 外框、feature card |
| radius x-large | `1→1.5rem` fluid | 大型 media/card |
| radius xx-large | `1→2rem` fluid | hero/section 級大型容器 |
| border main | `.0625rem` | 官網標準 1px 邊框 |
| focus width | `.125rem` | focus ring 2px |
| focus offsets | inner `-.125rem` / outer `.25rem` | 內外 focus 樣式 |

陰影方面，官網主要維持低對比：卡片、marginalia、浮動元素常見 `0 4px 24px #0000000d` 這類約 5% 黑的柔陰影；不把陰影作為主要層級語言，層級多靠底色、邊框、spacing 與 typography 建立。

## 6. 動效系統

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

### 6.35 ⚡ `cds-*` 動畫命名空間

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

## 7. 元件規格

### 7.0 ⚡ 2026-06 App 元件規格

| 元件 | 規格 |
|---|---|
| **Composer（聊天輸入卡）** | `border-radius: 20px`、`bg-000` 白底；外框不是實線 border，而是 `0 0 0 .5px hsl(border/30%)` 髮絲 ring + `0 4px 20px hsl(black/3.5%)` 柔陰影；內含 ＋鈕（Add files, connectors, and more）、模型＋思考強度選擇器（`Sonnet 4.6 · Low ⌄`）、麥克風（press-and-hold）與語音模式鈕。詳見 §7.18 |
| **App shell（側欄）** | 寬 `288px`、底色 `bg-100`（與主內容欄同底，靠髮絲線分隔）、右側 `0.5px hsl(border/15%)`；導覽項 `h-32 / 6×16px / radius 9px / 12px`；含 **Products 區（Code、Design）**。詳見 §7.17 |
| **模型選擇器（popover menu）** | 白底 `bg-000`、`radius 12px`、三層 popover 陰影；item `h-32 / 6×10px / radius 8 / 14px`（模型名＋說明＋✓），含 Effort、More models 子選單。詳見 §7.19 |
| **使用者訊息泡泡** | `border-radius: 12px`、底色 `#efeeeb`（= 新 `bg-300` / `_gray-50`）、內距 `10px 16px`、Anthropic Sans（`.font-large !font-user-message`） |
| **Claude 回覆** | Anthropic Serif `16px/24px` w400、容器 `.font-claude-response leading-[1.65rem]`、無泡泡直排；訊息操作列：Retry / Edit / Copy |
| **回覆內 pre 區塊** | `bg-bg-000/50` + `border-0.5 border-border-400` |
| **新對話問候** | 星芒（橘）+ serif 大字 `✳ Welcome, <名字>`，置中 |
| **風格 chips** | Code / Write / Learn / Life stuff / Claude's choice — pill 形、icon + 文字 |
| **促銷卡（右下）** | 白卡、插圖 + 標題 + 黑色 primary 按鈕 + 右上 X |
| **官網 Segmented selector** | Pricing 的 Individual / Team & Enterprise（Team and Enterprise）tabs：外層 `gray-150` + `1rem` radius，active 白底，`role=tablist` |
| **官網共用元件矩陣** | Webflow 主站與 `/docs/*` 文件頁共用的 nav、footer、card、filter、tabs、accordion、docs shell 等模式，詳見 §7.14–7.16 |

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

> 2026-06 起 `--accent-secondary-*` 已更名為 `--accent-*`（見 §2.0）。focus ring 與 §7.11 文字選取使用 `--accent-000/100`；本專案 `tokens.css` 保留舊名別名，故兩種寫法皆可。

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

### 7.12 Segmented Selector / Toggle Group（分段選擇，如 Individual / Team & Enterprise）

來源：`claude.com/pricing`。截圖中的 **Individual / Team and Enterprise**（目前文案為 **Team & Enterprise**）不是 native `<select>`，而是 Webflow tabs：

```html
<div data-tabs="menu" role="tablist" class="tab_menu_inner">
  <button data-tabs="tab" role="tab" class="tab_btn_wrap is-active">Individual</button>
  <button data-tabs="tab" role="tab" class="tab_btn_wrap">Team & Enterprise</button>
</div>
```

**官網大尺寸 selector（pricing tabs）**：

| 部位 | 規格 |
|---|---|
| 外層 | `display:flex`、`padding:.25rem`、`border-radius:var(--radius--large)` = `1rem` |
| 外層底色 | `theme--background-tertiary` = `gray-150 #f0eee6` |
| 選項 | `height:2.5rem`、`padding:.5rem 1rem`、`border-radius:var(--radius--main)` = `0.75rem` |
| 文字 | caption `0.75rem / 1.6`、Anthropic Sans regular、`white-space:nowrap` |
| 預設 | 透明底、文字 `foreground-tertiary` = `gray-600 #5e5d59` |
| hover | 底 `background-primary`、文字 `foreground-primary` |
| active | 底 `theme--white` / `gray-000 #fff`、文字 `foreground-primary`；hover 其他選項時 active 底暫時透明 |
| 行為 | `role=tablist` + `role=tab` + `aria-selected`；對應 panel 用 `role=tabpanel` |

實作類別見 `tokens-marketing.css` 的 `.mkt-segmented` / `.mkt-segmented__option`。

**App 小型 segmented control（外觀模式等）**：

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

### 7.14 claude.com 共用元件矩陣

claude.com 的 UI 由少數穩定元件與頁型重複組合而成。多語系頁面沿用同模板，因此元件規格以 canonical 頁與 docs 頁的共用模式判讀：

| UI 模式 | 主要 class / attribute | 設計規格與行為 | 覆蓋頁型 |
|---|---|---|---|
| **Global nav / mega menu** | `nav_wrap`、`nav_desktop_layout`、`nav_links_link`、`nav_dropdown_*`、`nav_actions_wrap` | nav 底 `theme--background-primary`，底部 `1px theme--border-tertiary`；desktop 內距 `1.5rem 0`；link 內距 `.5rem .5rem .5rem .75rem`；dropdown link `min-height:2.5rem`、`radius--x-small`；開合由 Webflow dropdown + GSAP，`300ms` 關 / `600ms` 開。 | 幾乎所有 Webflow 頁 |
| **Footer / locale picker** | `footer_links_layout`、`footer_link`、`locale_picker_dropdown_button` | footer links 4 欄（mobile 1 欄）；link 上下 `.5rem`；語系 dropdown `border:1px theme--border-tertiary`、`radius--main`、`.8em 1.4em`，hover 底 `background-tertiary`，focus ring `clay-interactive`。 | 全站 footer |
| **Clickable surface** | `clickable_wrap`、`clickable_link`、`clickable_btn`、`u-sr-only` | card/link 常用整面 absolute overlay；wrapper 繼承父層 radius，`link` 與 `button` 版本互斥；可視文字與 screen-reader label 分離。 | card、nav、footer、directory |
| **Button family** | `button_main_wrap`、`button_small_wrap`、`button_tiny_wrap`、`button_icon_main_wrap`、`button_icon_tiny_wrap`、`button_prompt_wrap` | main `min-height:2.5rem`、small `2.25rem`、tiny `1.75rem`、prompt `1.625rem`；內距 main `.5rem 1rem`、small `.5rem .75rem`；icon main `2.5rem square`、icon tiny `1.75rem square`；hover 以 `box-shadow` 模擬 border 加倍。 | CTA、nav、forms、prompt |
| **Form fields** | `w-form`、`form_main_field_copy`、`form_main_error_wrap`、`form_prompt_textarea` | Webflow form 狀態 `w-form-done` / `w-form-fail`；主要欄位 `height:3rem`、`padding:.5rem`、`border:1px theme--border-tertiary`、底 `background-primary`；textarea `min-height:5rem`、`max-height:10rem`；error 用透明底 + `error-text #b53333`。 | contact-sales、newsletter、prompt |
| **Prompt / Ask Claude module** | `data-claude-form`、`data-claude-textarea`、`prompt_menu_*`、`button_prompt_wrap` | 輕量 prompt 輸入框：透明 textarea + 右側小按鈕；menu `position:absolute; inset:0 0 auto`、`padding:.5rem`、`border/radius--main`、初始 `visibility:hidden`；item `padding:.75rem` + top border。 | 大量內容頁 footer/inline CTA |
| **Cards / link cards** | `card_link_wrap`、`card_link_visual`、`connector_card-*`、`marginalia_*` | link card `padding:space--2rem`、`radius--x-large`、`border-tertiary`、hover background/box-shadow；visual 方形 `radius--large`；marginalia card `13.5rem` 寬、`padding:1rem`、`radius--large`、`0 4px 24px rgb(0 0 0 / 5%)`。 | resources、customers、connectors、blog |
| **Directory filters** | `stories_filters`、`stories_filters_dropdown_wrap`、`data-filters-action`、`data-uc-filter` | 左側 filter 最大寬 `13.5rem`；每組 `padding:1rem 0` + top border；mobile/tablet variant 改為整寬 dropdown；category/tags 以 Webflow/Finsweet attribute 驅動。 | resources、customers、partners |
| **Tabs / segmented controls** | `data-tabs`、`tab_menu_inner`、`tab_btn_wrap`、`role=tablist` | 見 7.12；除 pricing 外，也出現在 solutions、healthcare、Chrome 等頁作內容分段。 | pricing、solutions、product |
| **Accordion / FAQ** | `data-accordion`、`faq_list`、`accordion_js` | FAQ 以 data attribute 驅動；每列用 border 分隔，heading 通常搭配 serif 或 body style；開合動畫由站內 JS/GSAP 控制，文件以 `slide-down/up 300ms` 表示。 | blog category、connectors、programs、download |
| **Slider / carousel** | Swiper 11、`swiper.is-marginalia`、`swiper-slide`、`data-slider-card` | 全站載入 Swiper；customer/marginalia slider `width:100%; overflow:hidden`，slide `flex:none; width:100%`；搭配 GSAP scroll/drag scripts。 | customer stories、solutions、partners |
| **Logo wall / partner wall** | `logo_wall_wrap`、`logo_wall_divider`、`data-logo-wall-item` | divider `height:1px`、`margin-bottom:space--4rem`、色 `border-tertiary`；logo target 置中；常與 `u-alignment-center`、`u-margin-inline-auto` 搭配。 | enterprise/solutions/partners |
| **Breadcrumb / detail header** | `breadcrumb_list`、`breadcrumb_text`、`hero_connector_*` | breadcrumb gap `.5rem`，caption，linked item hover underline；connector detail hero icon `5rem`、`border-tertiary`、`radius--main`；details 欄常置於 12 欄 grid 末段。 | connectors/plugins/resources detail |
| **Rich text / copy / code** | `u-rich-text-blog`、`command_card_wrap.is-copy`、Finsweet `copyclip` | blog inline `code` 底 `background-secondary` + inset `1px border-secondary` + `.2em/.5em`；copy command card 用 compact padding `.375rem 1rem`；部分頁載入 copyclip。 | blog、docs、code-with-claude |
| **Event agenda** | `cwc26_agenda_*` | agenda row desktop grid `12.5rem 1fr`、`gap:2rem`、上下 `2rem`、bottom border `border-primary`；tablet 約 `11.25rem 1fr`；mobile 改 column。 | code-with-claude |
| **Privacy / modal layer** | `privacy_choices_dialog`、`shared/webflow-privacy-banner.js` | 共享 privacy banner / choices dialog 跨 Webflow 頁載入；dialog 與 overlay 層級獨立於頁面內容，屬全站互動元件。 | 全 Webflow 頁 |
| **Docs shell** | `/docs/_next/*`、`data-component-part`、`data-state`、`data-table-wrapper`、`role=tablist` | docs 區不是 Webflow，而是 Next/Tailwind：左側 doc nav + content column；dark classes 常駐；code / table / callout / tabs 使用 `data-*` 狀態與 horizontal overflow wrapper。 | `/docs/*` 75 頁 |

### 7.15 claude.com docs（/docs）元件補充

`/docs/*` 路徑採 Next/Tailwind，不吃 `claude-brand.shared` Webflow CSS；穩定模式如下：

- **Docs shell**：左側導覽 + 主內容欄，常見 class `flex`、`items-center`、`min-w-0`、`relative`、`w-full`、`group`；active item 透過 `data-active` / `data-state`。
- **Tabs**：`role=tablist` + `role=tab` + `role=tabpanel`，用於多種 connector/cowork docs 範例切換。
- **Code / copy**：code block 保留深底、monospace、copy/floating buttons（`data-floating-buttons`）。
- **Tables**：`data-table-wrapper` 包住表格，確保小螢幕橫向捲動。
- **Callouts**：`data-callout-type` 標示提示類型；與正文同欄寬，不用浮動卡片。

### 7.16 文件涵蓋範圍

claude.com UI 規格不把每個 URL 逐頁列成元件，因為大部分是同模板內容變體；設計系統的涵蓋方式是：

1. **頁型** 放在 §10.3：以 route group 描述 directory、detail、pricing、docs、event、form 等版面骨架。
2. **元件** 放在 §7.12–7.15：以 class / `data-*` / role pattern 定義 tabs、cards、forms、filters、accordion、docs shell。
3. **tokens** 放在 `tokens-marketing.css` / `tokens.json`：提供可重用的參考 class，例如 `.mkt-card-link`、`.mkt-filter`、`.mkt-prompt`、`.mkt-docs-shell`。
4. **demo** 放在 `index.html` 第 7、10 章：用同一套 token 呈現代表性元件與頁型。

> §7.17–7.19 定義 App shell、composer 與模型選擇器；色彩 token 與 §2.0 的 App 色彩架構一致。

### 7.17 App shell（側欄）

claude.ai 的版面是「側欄 + 主內容欄」單層 shell，**側欄與主內容同底色**，僅靠髮絲線分隔（不靠變色製造層級）：

| 部位 | 規格 |
|---|---|
| 側欄寬 | `288px`（18rem） |
| 側欄底色 | `bg-100` = `#f8f8f6`（與頁面、主內容同底） |
| 右側分隔 | `border-right: 0.5px solid hsl(var(--border-300) / 15%)` |
| 導覽項 | `height 32px`、`padding 6px 16px`、`border-radius 9px`、`font-size 12px`、icon(20px) + label、hover 升一階底色 |
| 主內容欄 | 透明底（透出 `bg-100`），內容置中、`max-width` 限制行長 |

**側欄結構（由上而下）**：

1. 頂部：`Claude` 字標（連 `/new`）＋ Search 鈕＋ Close sidebar（收合）鈕
2. New chat（`+`）
3. 主導覽：Chats（`/recents`）、Projects、Artifacts、Customize
4. **Products 區**：Code（`/code`）、Design（`/design`）產品入口（小標題 `Products`）
5. Recents：對話清單，附「Group by」分組控制
6. 底部：帳號鈕（`<團隊名>, Settings`）＋「Get apps and extensions」
7. 右上角浮動：**Use incognito**（暫時對話／不留存，星芒鬼魂 icon）

### 7.18 Composer（聊天輸入卡）詳規

| 部位 | 規格 |
|---|---|
| 表面 | `bg-000` 白底、`border-radius: 20px` |
| 外框 | **非實線 border**，而是 ring + 柔陰影：`box-shadow: 0 0 0 .5px hsl(var(--border-300) / 30%), 0 4px 20px hsl(var(--always-black) / 3.5%)` |
| 輸入 | `role=textbox`（contenteditable）「Write your prompt to Claude」，placeholder `text-500` |
| ＋鈕 | aria「Add files, connectors, and more」、`32×32px`、`radius 8px` |
| 模型＋思考強度選擇器 | pill `height 32px`、`padding 0 8px 0 10px`、`radius 6px`、`font 12px`，文案如「Sonnet 4.6  Low ⌄」（見 §7.19） |
| 麥克風 / 語音 | press-and-hold 錄音鈕 + 語音模式鈕，皆 `32×32 / radius 8` icon button |
| 風格 chips（卡片下方） | `height 32px`、`padding 0 12px`、`radius 8px`、`font 14px`、透明底 + `1px` ring(`border/10%`)；內容 Code / Write / Learn / Life stuff / Claude's choice |

### 7.19 模型選擇器與彈出選單（popover menu）

點 composer 的模型 pill 展開 popover menu，此 elevation 是 App 全站選單/下拉的通用樣式：

| 部位 | 規格 |
|---|---|
| popover 容器 | `bg-000`、`border-radius: 12px`、寬約 `270px`、`padding 0` |
| popover 陰影（signature） | `0 0 0 1px hsl(var(--border-100) / 10%), 0 8px 24px hsl(var(--always-black) / 12%), 0 2px 6px hsl(var(--always-black) / 8%)` |
| 選項（`role=menuitem`） | `height 32px`、`padding 6px 10px`、`radius 8px`、`font 14px`、`gap 8px`；主行模型名 + 次行說明，選中右側顯示 ✓ |
| 子選單 | item 右側 chevron `>`：**Effort**（思考強度，當前 Low；取代舊「Max」字樣）、**More models** |

**模型清單**：Fable 5（最強，狀態可能暫不可用）、Opus 4.8（複雜任務）、Sonnet 4.6（日常高效，**預設選中**）、Haiku 4.5（最快）。此 popover 的 `12px` radius + 三層陰影即 §5.3 的 `--shadow-menu`。

### 7.20 設定（Settings）modal

設定不是獨立頁，而是 **置中 modal overlay**（`#settings/*` hash 路由，背景頁面變暗）：

| 部位 | 規格 |
|---|---|
| Modal 容器 | `bg-000`、`border-radius: 12px`、`960 × 720px`、`padding 0`；右上 `×` 關閉鈕 |
| Modal 陰影 | `0 0 0 1px hsl(border/10%), 0 1px 2px hsl(black/6%), 0 2px 8px hsl(black/8%)`（dialog elevation，比 menu 更收斂） |
| 版面 | 左側 nav rail（`~167px`，含 Search 框 + `Settings` 小標 + 分頁項）+ 右側可捲動內容欄 |
| 左 nav 分頁 | General / Account / Privacy / Billing / Usage / Capabilities / Connectors / Claude Code / Claude in Chrome；icon + label，active 項為填色 pill（升一階底 + 圓角） |
| 內容分區 | section 小標（如 Profile、Preferences）+ 設定列堆疊 |
| **設定列 pattern** | `display:flex; justify-content:space-between; align-items:center; padding:12px 0; border-bottom:1px hsl(near-black/5%)`——左 label、右 control，列間以 5% 淡 hairline 分隔 |
| 列內 control | 文字欄（右對齊）、ghost select（文字 + chevron、無框透明、右對齊、h32）、整寬 textarea（在 label 下方）、segmented icon control（Appearance＝系統/淺/深 三 icon）、ghost 觸發子頁 |

**設定列是 Claude「設定／表單頁」的標準解**：label 左、control 右、`12px` 上下內距、`5%` hairline 分隔、section 小標分組——§12.6 的 settings form recipe 即據此；Appearance 的三 icon segmented control 與本文件站自身的亮暗切換同型。

## 8. 圖示與品牌

### 8.0 ⚡ App SVG 圖示庫（assets/icons/，28 個）

App 內嵌 SVG 圖示全部使用 `currentColor`，可隨文字色階與主題變化：

| 類別 | 圖示 |
|---|---|
| 側欄/導覽 | `all-chats`、`search`、`caret-down`、`scroll-to-bottom`、`org-avatar-badge` |
| Composer ＋選單 | `add-files`、`screenshot`、`github`、`add-to-project`、`connectors`、`plugins`、`skills`、`globe` |
| 語音/聽寫 | `voice-mode`（音波條，rect 構成）、`dictation-done`、`dictation-cancel` |
| 風格 chips | `code`、`write`、`learn`、`life-stuff`、`claudes-choice`、`use-style` |
| 品牌/特殊 | `wordmark`（新版 viewBox `30 0 82 24`）、`spark-greeting`（100×100 星芒）、`incognito`（**內嵌 CSS 動畫**的幽靈圖示）、`cursor` / `cursor-glow`（Cowork 游標，glow 版帶 clay 色 drop-shadow） |

> 兩套尺寸體系並存：**Phosphor Icons**（`0 0 256 256`，風格 chips 等）與**自製 20×20 線性組**（UI 操作）。`assets/icons/_manifest.json` 有完整清單。

### 8.0.1 ⚡ UI 圖示主要使用「可變圖示字型 Anthropicons-Variable」

claude.ai 的多數 UI chrome 圖示不再是內嵌 SVG，而是透過可變圖示字型 `Anthropicons-Variable` 的字符（glyph）渲染。

| 項目 | 結論 |
|---|---|
| 字型 | `Anthropicons-Variable`（variable，weight `400 700`），woff2 經 `assets-proxy.anthropic.com/...woff2` 載入 |
| 元件 | 設計系統 `<span data-cds="Icon">`，`20px`、`currentColor`、`flex-shrink:0` |
| 字符 | Unicode 私用區（PUA，`U+E001`–`U+E100` 一帶）；單一設定/聊天頁就用到 27 個以上不同 glyph |
| 覆蓋模式 | `data-cds="Icon"` 主要渲染為字型 glyph；內嵌 `<svg>` 只保留在少數品牌、語音與特殊圖示 |
| 仍為內嵌 SVG 者 | 字標 `wordmark`、星芒 `spark-greeting`、語音音波 `voice-mode`、風格 chips（Phosphor `0 0 256 256`）、`incognito`、`cursor` |

**對本 repo 的意涵**：

- `assets/icons/` 的 `28` 個是**可被 DOM 擷取的內嵌-SVG 子集**（字標、星芒、Phosphor chips、語音、incognito 等），仍正確。
- 設定分頁（General / Account / Privacy / Billing / Usage / Capabilities / Connectors / Claude Code / Claude in Chrome）、側欄功能項（Projects / Artifacts / Customize / Design）、Appearance（系統/淺/深）等 UI chrome 圖示屬字型 glyph；若需要獨立資產檔，需以 `Anthropicons-Variable.woff2` 與 codepoint 對照表建立。
- 設計含義：取用 Claude 風格 UI icon 的官方做法已是「圖示字型 + currentColor」，可像文字一樣繼承顏色、字重與大小；自製時以 `20px`、`currentColor`、`flex-shrink:0` 的 inline icon 容器即與此對齊。

| 項目 | 規格 |
|---|---|
| **圖示庫** | [Phosphor Icons](https://phosphoricons.com)（`viewBox="0 0 256 256"`、`fill="currentColor"`），常用 16 / 20 / 24px |
| **Logo 字標** | 內嵌 SVG，`viewBox="0 0 112 24"`，`fill="currentColor"` + `text-text-000`（隨主題變色）；footer 另有 "By Anthropic" 標 |
| **品牌色** | Claude 橘 `#d97757`（`--accent-brand`） |
| **插圖** | 大型幾何線條 SVG（500×500 viewBox），單色 `text-text-000`，搭配 `--pictogram-*` 四階層次色 |
| **頭像/Spark** | Claude 星芒（starburst）標誌，橘底白芒或 currentColor |

### 8.2 官網 SVG 資產（claude.com → assets/marketing/，116 個）

| 類別 | 數量 | 說明 |
|---|---|---|
| `icon-*` | 93 | 16–24px UI/導覽圖示（20×20 為主，同 App 體系）；含 30 個 claude.com 跨頁共用圖示 |
| `pictogram-*` | 12 | 56–96 viewBox 產品圖標（pricing/download/security 頁） |
| `illustration-*` | 5 | 500×500 星芒系插圖 |
| `lockup-claude-code` | 1 | Claude Code 字標組合（573×125） |
| `social-*` | 4 | X / YouTube / LinkedIn / Instagram（16×16） |

另有 1462×674、1000×1000 大型 hero 場景圖未收錄（見 raw/ 頁面存檔）。

**claude.com 跨頁共用圖示**：官網圖示多為 `currentColor` 單色 SVG，可跟隨區段主題變色。已收錄的共用類型包含 globe、briefcase、gear、download、building、check-circle、shield-check、code、graduation cap、people、copy、external-link、chevrons、clipboard、trending 等。單頁第三方品牌商標不納入 Claude 設計系統圖示範圍。

### 8.3 claude.com 品牌資產使用模式

官網圖示/品牌使用模式如下：

| 訊號 | 出現方式 | 規格含義 |
|---|---|---|
| `u-svg` / `icon_wrap` | 在 canonical 頁大量出現 | SVG 多以 `currentColor` 跟隨區段主題，不應固定填色 |
| `nav_links_svg` / `nav_dropdown_icon` | 導覽與 mega menu | 20px 左右線性圖示，搭配文字與 divider |
| `logo_wall_*` / `data-logo-wall-item` | 客戶/合作夥伴牆 | logo 以灰階/單色呈現，靠 spacing 與欄寬建立節奏 |
| `lockup-claude-code` | Claude Code 產品頁與資產檔 | 產品 lockup 需視為品牌資產，不是一般圖示 |
| `social-*` | footer / social links | 16px 單色 SVG |
| `pictogram-*` / `illustration-*` | pricing、download、security 等頁 | 大尺寸 pictogram 用暖灰/品牌 accent 層次，不走多彩插圖風 |

結論：`assets/marketing/` 目前 116 個 SVG 覆蓋 shared component 的主要圖示類型；大型 hero 圖與 raster/srcset 圖片屬頁面內容資產，不作為核心 UI icon 規格。

## 9. 內容樣式：程式碼、Markdown、數學式

### 9.1 程式碼區塊（highlight.js — One Dark 配色，亮暗模式同用）

```css
.hljs { color: #abb2bf; background: #282c34; }   /* 永遠深底，是版面上的「深色島」 */
pre code.hljs { display: block; overflow-x: auto; padding: 1em; }
code.hljs { padding: 3px 5px; }
.hljs-comment, .hljs-quote { color: #5c6370; font-style: italic; }
.hljs-keyword, .hljs-doctag, .hljs-formula { color: #c678dd; }
.hljs-name, .hljs-section, .hljs-selector-tag, .hljs-deletion, .hljs-subst { color: #e06c75; }
.hljs-literal { color: #56b6c2; }
.hljs-string, .hljs-addition, .hljs-attribute, .hljs-regexp { color: #98c379; }
.hljs-attr, .hljs-number, .hljs-variable, .hljs-type, .hljs-template-variable { color: #d19a66; }
.hljs-bullet, .hljs-link, .hljs-meta, .hljs-symbol, .hljs-title { color: #61aeee; }
.hljs-built_in, .hljs-title.class_ { color: #e6c07b; }
.hljs-emphasis { font-style: italic; } .hljs-strong { font-weight: 700; }
```

行內 code / 程式碼字級：`0.813rem`、行高 1.5、Anthropic Mono w400（dark w360）；2026-01 存檔中的 JetBrains Mono 只作歷史對照。

### 9.2 Markdown（`.ReactMarkdown` 容器）

- 表格：`display: block; overflow-x: scroll`；`td, th { border: 1px solid #d5d3d1; padding: .5rem }`
- 編輯器（ProseMirror）表格：th 文字 `#43403c`、td 文字 `#77716d`、字級 `.875rem`
- AI 回覆容器使用 `font-claude-response`（襯線、w400/dark 360）

### 9.3 數學式

KaTeX（標準樣式表，`.katex-display` 置中區塊）。

### 9.4 claude.com docs / rich text

`claude.com` 有兩套內容樣式來源：主站 Webflow rich text 與 `/docs/*` Next/Tailwind 文件殼。

| 區塊 | 主要訊號 | 設計規格 |
|---|---|---|
| Webflow rich text | `u-rich-text-blog`、`rich_text`、blog/resource templates | 文章內文使用官網 typography token；行內 code 以淡底、細邊框、mono 字呈現，不是整塊 One Dark |
| Blog / resources | `/blog/detail`、`/resources/detail` templates | 文章頁保留 editorial spacing、metadata、tag、share/link controls |
| Docs shell | `/docs/*` 75 頁；Tailwind classes `text-sm`、`rounded-xl`、`ring-*`、`toc-item` | 左側 docs nav + 右側 TOC + 中央 content column |
| Docs table | `data-table-wrapper` | 表格外層可橫向捲動，避免小螢幕破版 |
| Docs callout | `data-callout-type` | callout 以淡底、ring/border 與 icon/label 建立層級 |
| Copy / anchor controls | `data-floating-buttons`、`group-hover:opacity-100`、`focus:opacity-100` | heading anchor、copy button 平時隱藏，hover/focus 顯示 |
| Code card | docs code blocks + copy controls | 技術文件 code block 是 docs shell 元件；App 的 highlight.js One Dark 仍作 App 端規格 |

結論：內容樣式需要同時涵蓋 App 的 `.ReactMarkdown` / highlight.js、官網 Webflow rich text，以及 `/docs` 文件殼的 callout、table、copy button。

## 10. 頁面結構模式

### 10.1 Landing（claude.ai 未登入首頁；2026-01 歷史存檔，現行官網見 10.3）

```
<nav>  fixed top-0 bg-bg-100 z-header — Logo | Meet Claude | Platform | Solutions | Pricing | Learn | Contact sales | [Try Claude]
<hero> 置中 — font-display 襯線大標（"Impossible? Possible."）
       + 登入卡：Continue with Google / 分隔線 or / Email 輸入 + Continue with email（h-11 大按鈕）/ SSO
<section> Meet Claude — font-claude-response 3xl~4xl 標題 + text-xl text-text-400 說明
<section> 功能卡片 — rounded-xl bg-bg-300 圖片容器（clamp() 流式尺寸）
<section> Explore plans — Toggle（Individual/Team）+ rounded-3xl 方案卡 ×3（Free/Pro/Max），強調卡帶藍光暈
<section> FAQ — Accordion（襯線標題）
<footer> bg-bg-500 — 多欄 sitemap + 法務連結
<toast>  cookie 同意 — bottom-right rounded-3xl bg-bg-500
```

### 10.2 App（聊天介面）骨架 — 取自 SSR 實際 DOM（2025-11 production）

```
body.bg-bg-100.text-text-100.font-ui.min-h-screen
└─ div.root
   └─ div.flex.min-h-full.w-full.overflow-x-clip          ← 整頁 flex 橫排
      ├─ div.shrink-0                                      ← 側欄欄位
      │  └─ div.fixed.z-sidebar.lg:sticky
      │     └─ nav[aria-label=Sidebar]
      │        .flex.flex-col.gap-3.pb-2.px-0.h-screen
      │        .border-r-0.5.border-border-300             ← 0.5px 髮絲右框、無底色差！
      │        .transition.duration-100
      │        ├─ div.flex.items-center.gap-px.p-2         ← 頂列：開合鈕（ghost icon）
      │        ├─ div.flex.flex-col.px-2.pt-1.gap-px.mb-6  ← 主導覽（New chat / Chats…
      │        │     ghost 按鈕樣式、項目間距 gap-px）
      │        ├─ div.flex-grow.overflow-y-auto.px-2.mb-2  ← Recents 滾動清單
      │        └─ div.px-1.5.pb-1                          ← 底部 user-menu 列
      └─ div.w-full.relative.min-w-0                       ← 主內容區
         ├─ div.absolute.top-3.right-3.z-header
         │     .flex.items-center.gap-3.5                  ← 右上浮動工具列
         └─ 對話流（max-w-3xl = 48rem 置中欄）
ol.fixed.right-0.top-0.z-toast.flex.flex-col.gap-4.p-4    ← App 通知在「右上」
```

**版面要點：**

- 側欄與主區**同底色**，分界只靠 `border-r-0.5` 髮絲線（`0.5px hsl(border-300/15%)`）
- **收合 = 3.05rem（49px）icon rail**、**展開 = 18rem（288px）**；設定頁為 **modal**（`#settings/general` hash 路由）；App 通知 toast 容器在右上
- 側欄 `fixed` + `lg:sticky`，收合時 `width:0`；展開動畫：內容物 `opacity-0 -translate-x-0.5 transition-all duration-200` 滑入
- 側欄寬度：rail `3.05rem` ↔ 釘選展開 `18rem`（inline style 切換、transition duration-200）
- 側欄項目一律 ghost 按鈕（hover `bg-300`、active/展開 `bg-300` + `text-100`），列間距 `gap-px`
- 主區 `w-full relative min-w-0`；右上工具列浮動（`absolute top-3 right-3 z-header gap-3.5`），非整條 top bar
- 對話欄 `max-w-3xl`（48rem）置中；使用者訊息 sans + `bg-bg-300` 圓角泡泡，Claude 回覆 serif 直排無泡泡
- 本文件站沿用 App shell，但為 GitHub Pages 閱讀性將內容欄由 48rem 起跳，寬螢幕漸進放寬至 72rem（簡報模式 84rem）；這是文件站適配，不是 production 對話欄量測值
- Composer：`rounded-2xl bg-bg-000 border-0.5` + 陰影（登入後渲染，此項依 CSS 類推定）
- App 通知 toast 容器在**右上**；landing 的 cookie toast 才在右下

**App 功能頁版面（非聊天頁）**：claude.ai 的非聊天頁（Projects、Artifacts、Chats…）共用同一套 list/collection 骨架，掛在 App shell 主內容欄內：

| 區塊 | 規格 |
|---|---|
| 頁首列 | 左：serif 頁名大標（Anthropic Serif）；右：次要控制（如 `Sort by … ⌄` ghost 選單）+ 主要行動黑底鈕（New project / New artifact） |
| 搜尋列 | 頁首下方整寬 `bg-000` 圓角輸入框 + 放大鏡 icon + placeholder |
| 內容 | collection card grid（如 Projects 卡＝標題 + tag pill + 描述 + `Updated <date>` metadata、hairline 邊框）；無資料時走 empty state |
| Empty state | 置中：單色線稿 pictogram + serif 短標（如「What will you build with artifacts?」）+ sans 說明 + 一個行動鈕 |

**Customize 頁（list/detail 變體）**：進入時主側欄**收合成 `49px` icon rail**，旁邊展開 `255px` 次級導覽面板（Skills / Connectors / Personal plugins）；主內容為置中 pictogram + serif 大標（`24px / w500`）+ 說明 + 行動卡清單（每張卡＝圓形 icon 容器 + 標題 + 說明，整列 hairline 邊框 + 圓角）。這是 App「icon rail + 次級面板 + 內容」的 list/detail 範式。

**設定（Settings）= modal overlay**：`#settings/*` hash 路由，不離開當前頁；詳規見 §7.20。

### 10.3 行銷官網（claude.com）站點與版面

> 行銷內容已從 claude.ai 遷至 claude.com（Webflow）；§10.1 的 claude.ai landing 為 2026-01 歷史存檔。

- 上方橫排 nav（`nav_desktop_layout`），下拉 `300/600ms` + `ease-expo-out`
- 產品線：Claude / **Claude Code**（+Enterprise）/ **Claude Cowork** / **Claude Security** / Claude for Chrome・Slack・M365 / Skills；模型頁 Opus・Sonnet・Haiku
- 站點規模：`1,520` 個 canonical marketing pages、`75` 個 docs pages，另有 `/de` `/fr` `/ja` `/ko` 多語系模板變體
- 核心品牌/產品頁：`/product/*`、`/platform/*`、`/solutions/*`、`/partners/*`、`/pricing`、`/download`、`/skills`、`/plugins`、`/connectors`；多語系 `/de` `/fr` `/ja` `/ko`。
- 版面：區段堆疊 + `.u-theme-*` 換膚；hero 用 serif display 流式大字；技術棧 Webflow + Swiper

**Route groups（canonical + localized）**：

| Route group | 全 sitemap URLs | canonical URLs | 主要版面 / UI |
|---|---:|---:|---|
| `/connectors` | 639 | 366 | directory + detail；breadcrumb、connector hero、metadata/details、related cards、FAQ |
| `/plugins` | 330 | 273 | directory + detail；與 connectors 同骨架，卡片/分類/CTA 密度較高 |
| `/resources` | 462 | 249 | use cases / tutorials / courses；filter sidebar、collection grid、article cards |
| `/customers` | 926 | 240 | stories directory/detail；customer cards、marginalia carousel、logo/quote modules |
| `/blog` | 355 | 165 | blog index/category/detail；rich text、inline code、related cards、category tabs |
| `/code-with-claude` | 156 | 149 | event pages；agenda grid、speaker cards、tags、Lottie/Swiper media |
| `/contact-sales` | 38 | 19 | form-heavy pages；Webflow form states、textarea/select、error/success states |
| `/solutions` | 62 | 13 | industry/use-case landing；hero, logo wall, cards, tabs, sliders |
| `/lp` | 7 | 7 | campaign pages；lead capture / focused CTA / custom hero modules |
| `/partners` | 24 | 6 | partner index/detail；logo walls、filter/directory、pattern background |
| `/product` | 20 | 4 | product overview/model pages；hero + product modules + comparison cards |
| `/platform` | 10 | 2 | API / model platform pages；technical cards、CTA bands |
| `/programs` | 10 | 2 | startups/campus；program landing + FAQ / logo wall |
| `/community` | 5 | 2 | ambassador/community pages；application CTA + cards |
| singleton pages | — | 13 | `/pricing`、`/download`、`/skills`、`/claude-for-chrome`、`/claude-for-slack`、`/claude-for-microsoft-365`、`/fast-mode`、`/healthcare-administration` 等 |
| `/docs/*` | 75 | 75 | Next/Tailwind docs shell；sidebar、tabs、tables、code copy、callouts |

**頁型版面模式**：

| 頁型 | 排版骨架 | 代表 UI |
|---|---|---|
| Marketing landing | full-width section stack；`site--margin` + `max-width--main 90rem`；hero 大多置中或 12 欄切分 | nav / mega menu、hero CTA、logo wall、card grid、FAQ accordion、footer |
| Directory/listing | header + 12 欄內容；左 filter（最大 `13.5rem`）+ 右 collection grid；mobile filter 轉 dropdown | filter groups、collection cards、tags/chips、pagination/related |
| Detail/article | breadcrumb + hero；內容欄與 aside/details 欄；底部 related cards | breadcrumb、rich text、inline code、copy command、related cards |
| Pricing | tabbed audience selector + pricing card grid + FAQ | segmented selector、pricing cards、feature lists、accordion |
| Form/campaign | section stack + form panel / Webflow states | form fields、textarea/select、success/fail、privacy banner |
| Event | agenda grid + speakers + sponsor/media modules | agenda row、tag pills、Lottie、Swiper, speaker cards |
| Docs | app-like docs shell；sticky/scrolling nav + prose column | side nav、tabs、callouts、tables、code blocks、copy/floating actions |

**全站共用腳本與互動層**：

- Webflow 頁共用 Swiper 11、jQuery、GSAP `3.15.0`、ScrollTrigger、SplitText、TextPlugin、Flip、Draggable、InertiaPlugin、Lottie / dotlottie、privacy banner、custom tracking。
- `@finsweet/attributes` 出現在 collection/filter/copy 等頁；`copyclip` 主要用於可複製指令或分享連結。
- 主要 shared CSS：`claude-brand.shared.9ce205edd.min.css`；少數 singleton / Next 頁有獨立 CSS bundle。

**覆蓋結論**：

- claude.com 的 UI 不以「每個 URL 一套設計」存在，而是以 route group + Webflow component variants 重複組合；route group 的 page pattern 見 §10，共用元件與互動規格見 §7。
- 多語系頁只改文字長度與 locale path，不使用獨立 UI 系統；實作時需保留較長德文/法文標籤的 wrapping。
- `/docs/*` 與 Webflow marketing 使用不同技術棧，已獨立列入 §7.15 與本章 docs 頁型。

## 11. 實作指南

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

## 12. 新 UI 設計推導指南

本章不是再列一個元件，而是回答：「如果要做的 UI 沒有出現在前面章節，該怎麼用 Claude 的語言設計出來？」

Claude-style UI 的核心不是某個固定外觀，而是一組穩定決策：**先判斷場景密度，再選版面骨架，最後用 token、狀態、互動規則收斂細節**。

### 12.1 先判斷 Surface：App、Marketing、Docs 或 Hybrid

| Surface | 適用場景 | 使用規則 |
|---|---|---|
| App / Tool（claude.ai） | 工作台、聊天、設定、資料管理、內部工具、SaaS dashboard | 用 `tokens.css`、`data-mode`、低對比表面、密集但安靜的工具列與側欄 |
| Marketing（claude.com Webflow） | 官網 landing、產品頁、pricing、customer story、活動頁 | 用 `tokens-marketing.css`、`.u-theme-*` 區段換膚、流式大字、section stack、CTA |
| Docs（claude.com/docs） | 技術文件、API guide、教學、reference | 左側 nav + 中央內容 + 右側 TOC；code/table/callout/copy controls 優先 |
| Hybrid | 產品內教學、文件化工具、可操作的 reference site | 以 App shell 承載內容，局部使用 docs 的 content pattern；不要混用 marketing hero 作為工作介面 |

**判斷口訣**：使用者來這裡是要「完成工作」就用 App；要「理解產品價值」就用 Marketing；要「查資料與照做」就用 Docs。

### 12.2 新 UI 的 8 步決策流程

1. **定義任務密度**：使用者是掃描、比較、輸入、閱讀、選擇，還是執行命令？
2. **選 Surface**：App / Marketing / Docs / Hybrid 只選一個作為主系統。
3. **選版面骨架**：sidebar shell、content column、list/detail、form page、section stack、docs shell。
4. **選資料呈現型態**：row、table、card grid、tabs、segmented selector、accordion、callout、modal。
5. **套 token**：先決定背景階層，再決定文字、邊框透明度、radius、spacing、shadow。
6. **補 states**：default、hover、active、focus-visible、disabled、loading、empty、error、success。
7. **補 interaction**：100–200ms transition、press `scale(.985)`、大面積展開 250–300ms、尊重 `motion-reduce`。
8. **做自我檢查**：對照 §12.8 checklist；若有一項偏離，先改 token/階層，不要先加裝飾。

### 12.3 版面骨架選擇表

| 需求 | 首選骨架 | 避免 |
|---|---|---|
| 長時間操作 / 反覆使用 | App shell：側欄 + 主內容 + 右上工具列 | marketing hero、大型插圖、過多卡片包裝 |
| 資料列表 / 管理後台 | toolbar + filter + table/list + detail drawer/modal | 每筆資料都做大卡片、重陰影、彩色分類過多 |
| 設定頁 / 表單 | single content column + section group + inline validation | 把每個欄位包成 card、把錯誤藏在 toast |
| 內容閱讀 / 文件 | docs shell + TOC + callout/code/table | 過寬行長、缺少 anchor/copy、表格不能橫捲 |
| 產品介紹 / Landing | full-width section stack + fluid display type + CTA | App-style dense toolbar、狹窄文件欄 |
| Directory / Marketplace | filter sidebar + collection grid + detail cards | 無篩選的大瀑布流、hover 才看得到核心資訊 |
| Pricing / plan choice | segmented selector + plan cards + feature comparison | native select、過度裝飾的 tab |

### 12.4 未知元件的 Anatomy 推導

任何新元件都先拆成 anatomy，再套 Claude 的尺寸與狀態：

| 部位 | 設計規則 |
|---|---|
| Container | 背景用 `bg-000` / `bg-100` / 官網 `background-primary/secondary`；邊框用低透明，不用實灰 |
| Header | serif 用於章節/內容標題；工具列與 label 用 sans；heading 不過度放大 |
| Body | 內文用 `text-200`，輔助說明用 `text-400/500`；行高保持 1.5–1.7 |
| Action | 主要行動最多 1 個；secondary/ghost 放旁邊；danger 只在破壞性操作 |
| Icon | `currentColor`，16/20/24px；跟文字同色階，不單獨上彩色 |
| Divider | `0.5px` 或 `1px` + `border-* / 10–30%`；不用大面積分隔線 |
| Feedback | inline 優先；toast 只回報全域或非阻塞結果 |

### 12.5 狀態矩陣

| State | 規則 |
|---|---|
| Default | 低對比、清楚但不搶；不要預設高彩度 |
| Hover | 背景升一階或文字變強；官網 CTA 可用 border/spacer 變化 |
| Active / Pressed | `scale(.985)` 或背景再升一階；不要大幅位移 |
| Focus-visible | 2px ring；App 用藍系互動色，官網用 focus token |
| Disabled | opacity 約 `0.5`，保留 layout 尺寸 |
| Loading | spinner / skeleton / shimmer；不要讓內容區跳動 |
| Empty | 給下一步 action；插圖若有，單色或低彩度 |
| Error | inline 說明 + `danger` / `error-text`；表單錯誤靠欄位附近，不只 toast |
| Success | 非阻塞結果用 toast；完成狀態可用淡底 + check icon |

### 12.6 常見新 UI Recipe

| UI 類型 | Claude-style 做法 |
|---|---|
| Data table | `bg-000` 表面、細分隔線、compact row、hover 淡底、右側 row actions；大量操作放 toolbar |
| Dashboard | 以資訊密度為主，少用 hero；metric 可用小 panel，但不要每個指標都重陰影 |
| Command palette | modal overlay + `bg-000` panel + search input + row list；focus/keyboard state 清楚 |
| File picker | drop zone 用 dashed/low alpha border；檔案列用 row，不用大型卡片堆疊 |
| Filter panel | 左側或頂部 filter group；chip/tag 用 `bg-300`、小字、低對比邊框 |
| Empty state | serif short heading + sans explanation + 一個 primary action；插圖最多單色 pictogram |
| Settings form | section heading + description + fields；儲存按鈕 sticky 或 section end；錯誤 inline |
| Onboarding checklist | row list + progress indicator；完成項降低對比，不用彩色 confetti |
| AI composer | 大圓角輸入表面、底部工具列、icon button、prompt menu；textarea 高度穩定 |
| Permission dialog | modal + 明確原因 + primary/secondary action；危險權限用 warning/danger，不用恐嚇式紅色大面積 |

### 12.7 Do / Don’t

| Do | Don’t |
|---|---|
| 用 token 階層決定表面，不臨時挑色 | 到處使用純白、純黑、實灰框 |
| 用 `currentColor` SVG，讓 icon 跟隨文字 | 為每個 icon 指派不同品牌色 |
| 讓工作型 UI 安靜、密集、可掃描 | 把 SaaS / dashboard 做成 landing page |
| 一頁只強調一個主要行動 | 每個區塊都放橘色 CTA |
| 用 inline error / empty / loading 保持流程 | 所有回饋都丟 toast |
| 用 section、row、table 承載結構 | 卡片再包卡片、陰影堆陰影 |
| 對未知元件補完整 state | 只做 default 畫面 |
| 多語系與長 label 預留 wrapping | 假設英文短字永遠不換行 |

### 12.8 設計交付 Checklist

每個新 UI 完成前，至少檢查：

- Surface 已選定：App / Marketing / Docs / Hybrid。
- 背景、文字、邊框、radius、shadow 都來自 token。
- 有 hover、active、focus-visible、disabled、loading、empty、error。
- text 不靠 viewport width 縮放；長字與多語系 label 不溢出。
- 互動動效在 100–300ms 範圍內，並尊重 `motion-reduce`。
- icon 是 `currentColor`，尺寸在 16/20/24px 或明確 pictogram 尺寸。
- 工作型頁面沒有不必要 hero、裝飾圖、重陰影或過大標題。
- 表格、code、長內容有 horizontal overflow 或合理 wrapping。
- 行動版不需要 hover 才能看到重要資訊。
- 若 demo 網頁本身呈現該 UI，demo 也必須通過同一份 checklist。

### 12.9 本文件站的自我約束

本 demo 網頁本身採 **Hybrid / App-style docs shell**：左側 App sidebar、右上浮動工具列、中央文件內容欄。它應遵守：

| 項目 | 自我約束 |
|---|---|
| Content surface | 每張 slide 只用一個主要文件表面；內部只在「實際元件 demo / repeated item」時使用 card |
| Background | 全站 `bg-100`，文件表面 `bg-000`；sidebar 與主區同底色 |
| Border | sidebar、table、demo、mirror item 都使用 hairline + 低透明 |
| Typography | slide 標題與章節標題用 serif；控制項、表格、label 用 sans/mono |
| Motion | slide 切換、hover、press 都在 100–350ms；沒有裝飾性長動畫 |
| Controls | Appearance segmented control、topbar icon buttons、A2 expand/collapse 都要有 focus-visible / keyboard 可用性 |
| Searchability | 章節標題、元件名稱與關鍵 token 保留可搜尋文字，不只靠截圖或圖示 |

若 demo 呈現某個設計規則，它本身也必須遵守同一規則；只有在 demo 是為了展示「反例」時才允許偏離，且必須明確標註。

### 12.10 表面高程階梯與雙模式推導

§12.2 第 5 步「套 token」最常被做錯，因為「用 token 階層決定表面」太抽象。具體規則：

**表面只有兩到三階，靠 token 與 hairline，不靠陰影**：

| 層 | App token | 官網 token | 用途 |
|---|---|---|---|
| 頁面底 | `bg-100` | `background-secondary` | 視窗背景、側欄（與內容同底） |
| 主要內容面 / 卡片 / 輸入 | `bg-000` | `background-primary` | 承載內容的表面（白／暗模式最亮階） |
| 巢狀 / hover / pressed | `bg-200`→`bg-300` | `background-tertiary` | 互動回饋、被選列、區塊底 |

- 一個畫面內高程差**不超過兩階**；需要更多層次時，先用 spacing 與 `0.5px` hairline（`border/10–30%`）分隔，不要靠加深灰框或疊陰影。
- 陰影只給「浮在內容之上」的層（menu、composer、modal、toast），用 §5.3 既有 recipe（`--shadow-menu` / `--shadow-composer`），不自創重陰影。

**Light-first → Dark 推導**（Claude 的實際做法）：先把亮模式做對，再機械式推導暗模式——

1. 背景整體加深（`bg-000` 由白變暗模式最亮階，頁面底更深）。
2. 字重整體調降約 `30–40`（亮字在深底會視覺增粗，見 §3.3）。
3. accent 提亮（藍 `accent-000` 由深藍 `#184e95`→亮藍、紫變亮），但**品牌橘 clay `#d97757` 維持不變**。
4. 邊框由深色改亮色 + 低透明度；陰影透明度加重（亮 4–8% 黑 → 暗 20–25%）。

→ 不要為暗模式重新挑色；暗模式是亮模式的「token 對調」結果。

### 12.11 三個簽名決策（最容易做錯、最能識別 Claude）

1. **serif 給內容、sans 給介面、mono 給程式碼**：襯線（Anthropic Serif）只用於 AI 回覆內文、editorial 標題、章節大標；所有工具列、按鈕、label、表格、設定欄一律無襯線（Anthropic Sans）；程式碼用 mono。把襯線用在密集工具 chrome 上會立刻「不像 Claude」。
2. **動效用命名 token，不要隨手填數字**：press `active:scale(.985)`；hover / 顏色過場 `100–200ms`；展開、簾幕用 `cubic-bezier(0.16,1,0.3,1)`（expo-out），nav 下拉 `300ms 關 / 600ms 開` 的不對稱；大面積 `250–300ms`；一律提供 `motion-reduce` 退場。見 §6。
3. **可及性底線**：文字對比達 WCAG AA（一般 ≥4.5:1、大字 ≥3:1）；互動熱區 `≥40px`（App row 視覺 32–36px，仍需 ≥40px 觸控區；官網按鈕 `2.5rem`）；focus-visible 永遠保留 2px ring；狀態不可只靠顏色（錯誤要有 icon／文字、選中要有 ✓ 或粗體）。

### 12.12 完整推導範例：設計一個文件沒有的「成員管理表格頁」

照 §12.2 八步走一次，示範如何只用既有規則推導出一個不在文件裡的頁面：

1. **任務密度**：掃描 + 比較 + 偶爾編輯 → 高密度管理型。
2. **Surface**：工作型、非 editorial → App，用 `tokens.css` + App shell（§7.17）。
3. **骨架**：側欄 + 主內容欄；頂部 toolbar（搜尋 + 篩選 +「邀請成員」primary）＋ 資料表格；編輯走 row action → drawer/modal，不整頁跳轉。
4. **資料呈現**：table（不是卡片堆疊）——`bg-000` 表面、row `h≈44px`、欄間細分隔線（`border/10–15%`）、hover 淡底（`bg-200`）、右側 row actions 用 ghost icon button。
5. **Token**：頁面 `bg-100`、表格面 `bg-000`、主文字 `text-200`、metadata `text-400`、邊框 hairline；圓角沿用 `8–12px`；唯一 primary（邀請）用黑底白字按鈕；危險（移除成員）走 `[data-color-context=danger]` 的 ghost 紅。
6. **States**：空狀態（serif 短標 +「邀請第一位成員」primary）、loading（skeleton rows，不要整頁 spinner）、row hover / selected、邀請失敗 inline error、成功用 toast。
7. **互動**：row hover `100–150ms` 淡底；drawer 展開 `250–300ms` expo-out；press `scale(.985)`。
8. **自我檢查**：對照 §12.8——沒有 hero、沒有彩色分類氾濫、沒有卡片包卡片、表格可橫向捲動、深淺兩模式都成立。

> 重點：全程沒有「發明新樣式」，而是用既有 token、骨架與狀態規則，把一個原本不在文件裡的頁面推導出來——這正是本章要的能力。

## 附錄：資料來源與檔案

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
