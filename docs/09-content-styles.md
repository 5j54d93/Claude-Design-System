# 09. 內容樣式：程式碼、Markdown、數學式

> 所屬文件：[INDEX](./INDEX.md)

---

### 9.1 程式碼區塊（highlight.js — One Dark 配色，亮暗模式同用）

```css
.hljs { color: #abb2bf; background: #282c34; }   /* 永遠深底，是版面上的「深色島」 */
pre code.hljs { display: block; overflow-x: auto; padding: 1em; }
code.hljs { padding: 3px 5px; }
.hljs-comment, .hljs-quote { color: #5c6370; font-style: italic; }
.hljs-keyword, .hljs-doctag, .hljs-formula { color: #c678dd; }
.hljs-name, .hljs-section, .hljs-selector-tag, .hljs-deletion, .hljs-subst { color: #e06c75; }
.hljs-literal { color: #56b6c2; }
.hljs-string, .hljs-addition, .hljs-attribute, .hljs-regexp { color: #98c379; }
.hljs-attr, .hljs-number, .hljs-variable, .hljs-type, .hljs-template-variable { color: #d19a66; }
.hljs-bullet, .hljs-link, .hljs-meta, .hljs-symbol, .hljs-title { color: #61aeee; }
.hljs-built_in, .hljs-title.class_ { color: #e6c07b; }
.hljs-emphasis { font-style: italic; } .hljs-strong { font-weight: 700; }
```

行內 code / 程式碼字級：`0.813rem`、行高 1.5、Anthropic Mono w400（dark w360）；2026-01 存檔中的 JetBrains Mono 只作歷史對照。

### 9.2 Markdown（`.ReactMarkdown` 容器）

- 表格：`display: block; overflow-x: scroll`；`td, th { border: 1px solid #d5d3d1; padding: .5rem }`
- 編輯器（ProseMirror）表格：th 文字 `#43403c`、td 文字 `#77716d`、字級 `.875rem`
- AI 回覆容器使用 `font-claude-response`（襯線、w400/dark 360）

### 9.3 數學式

KaTeX（標準樣式表，`.katex-display` 置中區塊）。

### 9.4 claude.com docs / rich text 複核

full sitemap 顯示 `claude.com` 有兩套內容樣式來源：主站 Webflow rich text 與 `/docs/*` Next/Tailwind 文件殼。

| 區塊 | crawl 訊號 | 設計規格 |
|---|---|---|
| Webflow rich text | `u-rich-text-blog`、`rich_text`、blog/resource templates | 文章內文使用官網 typography token；行內 code 以淡底、細邊框、mono 字呈現，不是整塊 One Dark |
| Blog / resources | `/blog/detail`、`/resources/detail` templates | 文章頁保留 editorial spacing、metadata、tag、share/link controls |
| Docs shell | `/docs/*` 75 頁；Tailwind classes `text-sm`、`rounded-xl`、`ring-*`、`toc-item` | 左側 docs nav + 右側 TOC + 中央 content column |
| Docs table | `data-table-wrapper` | 表格外層可橫向捲動，避免小螢幕破版 |
| Docs callout | `data-callout-type` | callout 以淡底、ring/border 與 icon/label 建立層級 |
| Copy / anchor controls | `data-floating-buttons`、`group-hover:opacity-100`、`focus:opacity-100` | heading anchor、copy button 平時隱藏，hover/focus 顯示 |
| Code card | docs code blocks + copy controls | 技術文件 code block 是 docs shell 元件；App 的 highlight.js One Dark 仍作 App 端規格 |

結論：第 9 章不能只寫 App 的 `.ReactMarkdown` 和 highlight.js；網頁版已補入 Webflow rich text 與 `/docs` 文件殼樣式，搜尋「callout」「table」「copy button」時能在主章節找到。
