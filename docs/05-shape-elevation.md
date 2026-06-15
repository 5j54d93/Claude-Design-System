# 05. 圓角、邊框、陰影

> 所屬文件：[INDEX](./INDEX.md)

---

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

/* App 浮層 elevation（2026-06-15 live 實測） */
--shadow-menu:     0 0 0 1px hsl(var(--border-100) / 10%),
                   0 8px 24px hsl(var(--always-black) / 12%),
                   0 2px 6px hsl(var(--always-black) / 8%);   /* 選單/下拉 popover；搭配 radius 12px、bg-000 */
--shadow-composer: 0 0 0 .5px hsl(var(--border-300) / 30%),
                   0 4px 20px hsl(var(--always-black) / 3.5%); /* 聊天輸入卡：髮絲 ring + 柔陰影取代實線 border；radius 20px */
```

> **App 浮層慣例**：「浮在內容之上」的層（menu / dropdown / composer / modal / toast）才用陰影，且用上面的 `--shadow-menu` / `--shadow-composer` 這類「1px/0.5px 邊界 ring + 一道柔和投影」組合——ring 維持邊界清晰、投影量極小（亮模式 3.5~12% 黑）。一般卡片與表面仍只用 `0.5px` 髮絲線 + 底色分層，不疊陰影。

毛玻璃：`backdrop-blur` 常用 `8px / 12px / 14px / 16px / 24px / 40px`。

### 5.4 官網形狀與焦點複核（claude.com）

`9ce205edd` shared CSS 補確認官網有自己的 shape token，與 App 的 `0.5px` 髮絲線系統不同：

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
