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
