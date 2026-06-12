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

- **12 欄格線**、gutter `2rem`、site margin 流式 `2→4rem`；容器 `main 90rem / medium 74.5rem / small 60rem`
- **區段間距全流式**：small `4→6rem`、main `6→8rem`、large `8→12.5rem`、page-top `12→15rem`
- 斷點（Webflow）：`≤479` / `480–767` / `768–991` / `≥992`
- 邊框 `0.0625rem`；focus ring `0.125rem`；官網圓角另有流式 `x-large 1→1.5rem`、`xx-large 1→2rem`
