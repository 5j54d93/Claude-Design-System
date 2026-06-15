# 08. 圖示與品牌

> 所屬文件：[INDEX](./INDEX.md)

---

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

### 8.0.1 ⚡ 2026-06-15 重大發現：UI 圖示主要改用「可變圖示字型 Anthropicons-Variable」

2026-06-15 live 觀察（設定 modal、側欄、各功能頁）顯示：claude.ai 目前**絕大多數 UI chrome 圖示不再是內嵌 SVG，而是透過可變圖示字型 `Anthropicons-Variable` 的字符（glyph）渲染**。

| 項目 | 實測 |
|---|---|
| 字型 | `Anthropicons-Variable`（variable，weight `400 700`），woff2 經 `assets-proxy.anthropic.com/...woff2` 載入 |
| 元件 | 設計系統 `<span data-cds="Icon">`，`20px`、`currentColor`、`flex-shrink:0` |
| 字符 | Unicode 私用區（PUA，`U+E001`–`U+E100` 一帶）；單一設定/聊天頁就用到 27 個以上不同 glyph |
| 覆蓋率（/new + 設定頁實測） | 頁面上 `66/66` 個 `data-cds="Icon"` 全為字型 glyph；整份文件僅 `13` 個內嵌 `<svg>` |
| 仍為內嵌 SVG 者 | 字標 `wordmark`、星芒 `spark-greeting`、語音音波 `voice-mode`、風格 chips（Phosphor `0 0 256 256`）、`incognito`、`cursor` |

**對本 repo 的意涵**：

- `assets/icons/` 的 `28` 個是**可被 DOM 擷取的內嵌-SVG 子集**（字標、星芒、Phosphor chips、語音、incognito 等），仍正確。
- 設定分頁（General / Account / Privacy / Billing / Usage / Capabilities / Connectors / Claude Code / Claude in Chrome）、側欄功能項（Projects / Artifacts / Customize / Design）、Appearance（系統/淺/深）等**這次新看到的圖示是字型 glyph，無法用 DOM 抓成 SVG 檔**；要逐一「收錄」需另下載 `Anthropicons-Variable.woff2` 並建立 codepoint→名稱對照（屬資產下載任務，與 UI 規格 crawl 分開）。
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
