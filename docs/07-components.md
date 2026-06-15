# 07. 元件規格

> 所屬文件：[INDEX](./INDEX.md)

---

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

---

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
