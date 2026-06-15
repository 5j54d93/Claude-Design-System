# 10. 頁面結構模式

> 所屬文件：[INDEX](./INDEX.md)

---

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

**App 功能頁版面（非聊天頁；2026-06-15 live 實測）**：claude.ai 的非聊天頁（Projects、Artifacts、Chats…）共用同一套 list/collection 骨架，掛在 App shell 主內容欄內：

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
- 產品線（nav 實測）：Claude / **Claude Code**（+Enterprise）/ **Claude Cowork** / **Claude Security** / Claude for Chrome・Slack・M365 / Skills；模型頁 Opus・Sonnet・Haiku
- **2026-06-15 full sitemap crawl**：主 sitemap `3,125` URLs；canonical `1,520`、多語系 `1,605`（`/de` `/fr` `/ja` `/ko`）；docs sitemap `75` URLs。實際抓取 canonical + docs 共 `1,595` 頁，`1595/1595` 回 `200`，HTML 分析量約 `761.6 MB`。
- 核心品牌/產品頁：`/product/*`、`/platform/*`、`/solutions/*`、`/partners/*`、`/pricing`、`/download`、`/skills`、`/plugins`、`/connectors`；多語系 `/de` `/fr` `/ja` `/ko`。
- 版面：區段堆疊 + `.u-theme-*` 換膚；hero 用 serif display 流式大字；技術棧 Webflow + Swiper

**Route group inventory（canonical + localized 全量）**：

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
- 最新主要 shared CSS：`claude-brand.shared.9ce205edd.min.css`（本輪全站 1,517 個 Webflow canonical 頁引用）；少數 singleton / Next 頁有獨立 CSS bundle。

**覆蓋結論**：

- claude.com 的 UI 不以「每個 URL 一套設計」存在，而是以 route group + Webflow component variants 重複組合；本輪已把各 route group 的 page pattern 納入 §10，把共用元件與互動規格納入 §7。
- 多語系頁只改文字長度與 locale path，未發現獨立 UI 系統；文件仍需在實作時注意較長德文/法文標籤的 wrapping。
- `/docs/*` 與 Webflow marketing 使用不同技術棧，已獨立列入 §7.15 與本章 docs 頁型。
