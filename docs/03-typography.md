# 03. 字體排印

> 所屬文件：[INDEX](./INDEX.md)

---

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
