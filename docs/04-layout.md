# 04. 佈局：斷點、z-index、間距

> 所屬文件：[INDEX](./INDEX.md)

---

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

### 4.5 Full sitemap 佈局複核（claude.com）

最新 shared CSS 裡佈局 token 的完整組成：

| 類型 | Token / 值 | 用途 |
|---|---|---|
| viewport | `--site--viewport-min: 20`、`--site--viewport-max: 90` | `clamp()` 以 320px→1440px 內插 |
| container | `main 90rem`、`medium 74.5rem`、`small 60rem`、`full 100%` | hero、內容頁、docs/landing 依版型取用 |
| grid | 預設 `6` 欄 / `1rem` gutter | mobile-first；桌面 template 再擴成 12 欄 |
| section spacing | `none 0`、`small 4→6rem`、`main 6→8rem`、`large 8→12.5rem`、`page-top 12→15rem` | 官網區段垂直 rhythm |
| spacing scale | `.25/.5/.75/1/1.5rem` 固定，`2/2.5/3/4/5/6rem` 多為流式 | 卡片內距、logo wall、feature grid |

結論：本章原先「12 欄 / 2rem gutter」的描述只適合桌面 pattern，已改成 mobile-first `6/1rem` + desktop `12/2rem` 的雙層敘述。
