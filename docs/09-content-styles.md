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

行內 code / 程式碼字級：`0.813rem`、行高 1.5、JetBrains Mono w400（dark w360）。

### 9.2 Markdown（`.ReactMarkdown` 容器）

- 表格：`display: block; overflow-x: scroll`；`td, th { border: 1px solid #d5d3d1; padding: .5rem }`
- 編輯器（ProseMirror）表格：th 文字 `#43403c`、td 文字 `#77716d`、字級 `.875rem`
- AI 回覆容器使用 `font-claude-response`（襯線、w400/dark 360）

### 9.3 數學式

KaTeX（標準樣式表，`.katex-display` 置中區塊）。
