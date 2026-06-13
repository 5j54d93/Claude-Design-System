# Claude Design System 完整規格文件

> 來源：claude.ai **live 站實地抓取（2026-06-12，build `e9c073951b`，Chrome 登入狀態）** + 歷史 production CSS bundle（2025-11 ~ 2026-03）交叉驗證。
> Token 版本：**v2.2 — 2026-06 雙站整併版**（primitive 色板層 + 語意層）；行銷頁與 console 主題沿用 2026-01 存檔。
> 用途：依本文件即可重建與 claude.ai 視覺完全一致的網站。

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

---

## 2. 色彩系統

### 2.0 ⚡ 2026-06 架構改版：Primitive 色板層（live 實測）

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

**語意層映射與實測值（live 2026-06）**：

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

**官方具名品牌色（`--cds-*` 命名空間，live 抓取）**：

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

---

## 3. 字體排印

### 3.0 ⚡ 2026-06 更新（live 實測）

- 字體正名：**`"Anthropic Sans"` / `"Anthropic Serif"` / `"Anthropic Mono"`**（等寬字不再是 JetBrains Mono！）
- serif 後備鏈加入完整 CJK 字串：`Georgia, "Arial Hebrew", …, "Hiragino Sans", "Yu Gothic", "PingFang TC", "Microsoft JhengHei", "PingFang SC", "Microsoft YaHei", "Apple SD Gothic Neo", serif`
- 實測：body 16px Anthropic Sans；Claude 回覆 **Anthropic Serif 16px / 24px / w400**（`.font-claude-response leading-[1.65rem]`）；使用者訊息 `.font-large !font-user-message`（sans）

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
  --font-mono:            var(--font-jetbrains);
  --font-sans-serif:      var(--font-ui);
  --font-serif:           var(--font-ui-serif);
  --font-system:          system-ui, sans-serif;
}
body, html { font-family: var(--font-ui); }
```

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
| h3 | 1.75rem | 2.25rem | body-1 | 1.1875rem | 1.25rem |

固定級：body-2 `1.0625rem`、body-3 `0.9375rem`、caption `0.75rem`、micro `0.625rem`。行高刻度 `1/1.1/1.2/1.3/1.5/1.6/1.7`；**text-trim 模擬**（上 `.39em` 下 `.38em`）做首行精準對齊。

---

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

- **12 欄格線**、gutter `2rem`、site margin 流式 `2→4rem`；容器 `main 90rem / medium 74.5rem / small 60rem`
- **區段間距全流式**：small `4→6rem`、main `6→8rem`、large `8→12.5rem`、page-top `12→15rem`
- 斷點（Webflow）：`≤479` / `480–767` / `768–991` / `≥992`
- 邊框 `0.0625rem`；focus ring `0.125rem`；官網圓角另有流式 `x-large 1→1.5rem`、`xx-large 1→2rem`

---

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
```

毛玻璃：`backdrop-blur` 常用 `8px / 12px / 14px / 16px / 24px / 40px`。

---

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

---

## 7. 元件規格

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

---

## 8. 圖示與品牌

### 8.0 ⚡ 2026-06 live 圖示庫（assets/icons/，28 個去重實品）

從 live 站（/new、選單、設定、對話頁）蒐集去重後的實際 SVG，全部 `currentColor`：

| 類別 | 圖示 |
|---|---|
| 側欄/導覽 | `all-chats`、`search`、`caret-down`、`scroll-to-bottom`、`org-avatar-badge` |
| Composer ＋選單 | `add-files`、`screenshot`、`github`、`add-to-project`、`connectors`、`plugins`、`skills`、`globe` |
| 語音/聽寫 | `voice-mode`（音波條，rect 構成）、`dictation-done`、`dictation-cancel` |
| 風格 chips | `code`、`write`、`learn`、`life-stuff`、`claudes-choice`、`use-style` |
| 品牌/特殊 | `wordmark`（新版 viewBox `30 0 82 24`）、`spark-greeting`（100×100 星芒）、`incognito`（**內嵌 CSS 動畫**的幽靈圖示）、`cursor` / `cursor-glow`（Cowork 游標，glow 版帶 clay 色 drop-shadow） |

> 兩套尺寸體系並存：**Phosphor Icons**（`0 0 256 256`，風格 chips 等）與**自製 20×20 線性組**（UI 操作）。`assets/icons/_manifest.json` 有完整清單。


| 項目 | 規格 |
|---|---|
| **圖示庫** | [Phosphor Icons](https://phosphoricons.com)（`viewBox="0 0 256 256"`、`fill="currentColor"`），常用 16 / 20 / 24px |
| **Logo 字標** | 內嵌 SVG，`viewBox="0 0 112 24"`，`fill="currentColor"` + `text-text-000`（隨主題變色）；footer 另有 "By Anthropic" 標 |
| **品牌色** | Claude 橘 `#d97757`（`--accent-brand`） |
| **插圖** | 大型幾何線條 SVG（500×500 viewBox），單色 `text-text-000`，搭配 `--pictogram-*` 四階層次色 |
| **頭像/Spark** | Claude 星芒（starburst）標誌，橘底白芒或 currentColor |

### 8.2 官網 SVG 資產（claude.com → assets/marketing/，86 個）

| 類別 | 數量 | 說明 |
|---|---|---|
| `icon-*` | 63 | 16–24px UI/導覽圖示（20×20 為主，同 App 體系） |
| `pictogram-*` | 12 | 56–96 viewBox 產品圖標（pricing/download/security 頁） |
| `illustration-*` | 5 | 500×500 星芒系插圖 |
| `lockup-claude-code` | 1 | Claude Code 字標組合（573×125） |
| `social-*` | 4 | X / YouTube / LinkedIn / Instagram（16×16） |

另有 1462×674、1000×1000 大型 hero 場景圖未收錄（見 raw/ 頁面存檔）。

---

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

行內 code / 程式碼字級：`0.813rem`、行高 1.5、JetBrains Mono w400（dark w360）。

### 9.2 Markdown（`.ReactMarkdown` 容器）

- 表格：`display: block; overflow-x: scroll`；`td, th { border: 1px solid #d5d3d1; padding: .5rem }`
- 編輯器（ProseMirror）表格：th 文字 `#43403c`、td 文字 `#77716d`、字級 `.875rem`
- AI 回覆容器使用 `font-claude-response`（襯線、w400/dark 360）

### 9.3 數學式

KaTeX（標準樣式表，`.katex-display` 置中區塊）。

---

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

**版面要點（皆有 DOM/CSS 證據）：**

- 側欄與主區**同底色**，分界只靠 `border-r-0.5` 髮絲線（live 實測 `0.5px hsl(border-300/15%)`）
- ⚡ live 實測：**收合 = 3.05rem（49px）icon rail**（不再是 width:0）、**展開 = 18rem（288px）**；設定頁已改為 **modal**（`#settings/general` hash 路由）；App 通知 toast 容器在右上
- 側欄 `fixed` + `lg:sticky`，收合時 `width:0`；展開動畫：內容物 `opacity-0 -translate-x-0.5 transition-all duration-200` 滑入
- 側欄寬度（live 驗證）：rail `3.05rem` ↔ 釘選展開 `18rem`（inline style 切換、transition duration-200）
- 側欄項目一律 ghost 按鈕（hover `bg-300`、active/展開 `bg-300` + `text-100`），列間距 `gap-px`
- 主區 `w-full relative min-w-0`；右上工具列浮動（`absolute top-3 right-3 z-header gap-3.5`），非整條 top bar
- 對話欄 `max-w-3xl`（48rem）置中；使用者訊息 sans + `bg-bg-300` 圓角泡泡，Claude 回覆 serif 直排無泡泡
- 本文件站沿用 App shell，但為 GitHub Pages 閱讀性將內容欄由 48rem 起跳，寬螢幕漸進放寬至 72rem（簡報模式 84rem）；這是文件站適配，不是 production 對話欄量測值
- Composer：`rounded-2xl bg-bg-000 border-0.5` + 陰影（登入後渲染，此項依 CSS 類推定）
- App 通知 toast 容器在**右上**；landing 的 cookie toast 才在右下

### 10.3 行銷官網（claude.com）站點與版面

> 行銷內容已從 claude.ai 遷至 claude.com（Webflow）；§10.1 的 claude.ai landing 為 2026-01 歷史存檔。

- 上方橫排 nav（`nav_desktop_layout`），下拉 `300/600ms` + `ease-expo-out`
- 產品線（nav 實測）：Claude / **Claude Code**（+Enterprise）/ **Claude Cowork** / **Claude Security** / Claude for Chrome・Slack・M365 / Skills；模型頁 Opus・Sonnet・Haiku
- **45 個頁面**：`/product/*`、`/platform/*`、15 個 `/solutions/*`、`/partners/*`、`/resources/*`、`/pricing`、`/download`、`/skills`、`/plugins`、`/connectors`；多語系 `/de` `/fr` `/ja` `/ko`
- 版面：區段堆疊 + `.u-theme-*` 換膚；hero 用 serif display 流式大字；技術棧 Webflow + Swiper

---

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

---

## 附錄：資料來源與檔案

| 檔案 | 內容 |
|---|---|
| `tokens.css` | 可直接引用的完整變數 + 元件樣式表 |
| `tokens.json` | 機器可讀 token（W3C Design Tokens 風格） |
| `raw/` | 自 production 取回的原始 CSS（主題檔、Button 模組等），供查證 |

**Live 抓取（2026-06-12，v2.0 主要來源）**：Chrome 登入狀態實地抓取 claude.ai（build `e9c073951b`）——`/new`、`/recents`、`/projects`、對話頁、composer 各選單；完整 token dump 與 SVG 存於 `raw/live-2026-06/tokens-and-svgs.json`、圖示成品於 `assets/icons/`。

歷史 production bundles（2026-01 版基礎）：`6492ae367fc05efc.css`（主題 2026-01）、`da25616fcf863192.css`（主題 2025-11）、`d471dc031853b581.css`（行銷 Tailwind 262KB）、`4679f5511a5b0b9e.css`（App Tailwind 229KB）、`f614b4fc9a5ddba6.css` / `b3152fa1bdafab10.css`（Button 模組）、`c2fb8af2f7b87066.css`（字體）、`d6bd087f08a7f52f.css`（JetBrains Mono）、`6c432c275096a856.css`（navbar 簾幕）、`7bf97b7f00340a78.css`（Toast）、`acdaad1d23646914.css`（KaTeX）。
