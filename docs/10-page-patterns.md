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
- Composer：`rounded-2xl bg-bg-000 border-0.5` + 陰影（登入後渲染，此項依 CSS 類推定）
- App 通知 toast 容器在**右上**；landing 的 cookie toast 才在右下

### 10.3 行銷官網（claude.com）站點與版面

> 行銷內容已從 claude.ai 遷至 claude.com（Webflow）；§10.1 的 claude.ai landing 為 2026-01 歷史存檔。

- 上方橫排 nav（`nav_desktop_layout`），下拉 `300/600ms` + `ease-expo-out`
- 產品線（nav 實測）：Claude / **Claude Code**（+Enterprise）/ **Claude Cowork** / **Claude Security** / Claude for Chrome・Slack・M365 / Skills；模型頁 Opus・Sonnet・Haiku
- **45 個頁面**：`/product/*`、`/platform/*`、15 個 `/solutions/*`、`/partners/*`、`/resources/*`、`/pricing`、`/download`、`/skills`、`/plugins`、`/connectors`；多語系 `/de` `/fr` `/ja` `/ko`
- 版面：區段堆疊 + `.u-theme-*` 換膚；hero 用 serif display 流式大字；技術棧 Webflow + Swiper
