# 12. 新 UI 設計推導指南

> 所屬文件：[INDEX](./INDEX.md)

---

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
| Searchability | 文字版新增內容必須同步到 A2 全文鏡像，讓瀏覽器搜尋可找到 |

若文件新增了設計規則，而 demo 自己違反，優先修 demo；只有在 demo 是為了展示「反例」時才允許偏離，且必須明確標註。

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
