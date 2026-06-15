# Claude Design System

[![GitHub license](https://img.shields.io/github/license/5j54d93/Claude-Design-System)](https://github.com/5j54d93/Claude-Design-System/blob/main/LICENSE)
![GitHub watchers](https://img.shields.io/github/watchers/5j54d93/Claude-Design-System)
![GitHub Repo stars](https://img.shields.io/github/stars/5j54d93/Claude-Design-System)
![GitHub forks](https://img.shields.io/github/forks/5j54d93/Claude-Design-System)
![GitHub repo size](https://img.shields.io/github/repo-size/5j54d93/Claude-Design-System)
![Platform](https://img.shields.io/badge/platform-Web-lightgrey)

A design system reference and design playbook reverse-engineered from [**claude.ai**](https://claude.ai) (the app) and [**claude.com**](https://claude.com) (the marketing site) — design tokens, components, icons, typography, motion, page patterns, and rules for designing new Claude-style UI, documented in a static site that is itself built with these very tokens.

<img src="assets/seo/cover.png" width='100%' height='100%'/>

## Overview

1. [**Highlights**](https://github.com/5j54d93/Claude-Design-System#highlights)
2. [**The Documentation Site**](https://github.com/5j54d93/Claude-Design-System#the-documentation-site)
3. [**What's Inside**](https://github.com/5j54d93/Claude-Design-System#whats-inside)
4. [**Quick Start**](https://github.com/5j54d93/Claude-Design-System#quick-start)
5. [**10 Keys to the Claude Look**](https://github.com/5j54d93/Claude-Design-System#10-keys-to-the-claude-look)
6. [**How It Was Made**](https://github.com/5j54d93/Claude-Design-System#how-it-was-made)
7. [**License：MIT**](https://github.com/5j54d93/Claude-Design-System#licensemit)

## Highlights

One design language, two implementations — both fully documented and merged chapter by chapter:

| | **claude.ai (App)** | **claude.com (Marketing)** |
|---|---|---|
| Token architecture | 317 primitives（`--_gray-*`…）→ semantic layer | swatch palette → 8 `.u-theme-*` section themes |
| Page background | `#f8f8f6` | `#faf9f5` classic ivory |
| Type scale | fixed rem | fluid `clamp()`（320 → 1440px） |
| Dark mode | site-wide `data-mode` | per-section themes（dark-1 ~ 3） |
| `::selection` | blue | clay orange at 50% |
| Button hover | background shift | **border doubling** |

- **44+ semantic color tokens** per theme with light／dark／console variants — every HEX verified against production
- **Anthropic Sans／Serif／Mono** typography system, including the dark-mode font-weight compensation trick（bold 600 → 530）
- **17 keyframes + signature easings**：`scale(0.985)` button press, clip-path nav curtain, shimmer "thinking" text…
- **114 production SVGs**：28 app icons, 86 marketing assets, pictograms, the Claude spark & wordmark
- Component specs measured live：composer（20px radius）, user bubble（12px／`#efeeeb`）, sidebar（3.05rem rail ↔ 18rem pinned）, pricing segmented selector, buttons, cards, forms, filters, nav, docs shell, toasts…
- A **Design Playbook** for UI not explicitly covered by the component catalogue：surface selection, layout recipes, unknown-component anatomy, state matrix, Do／Don’t, and a demo-site self-audit checklist

## The Documentation Site

Open [**index.html**](index.html) directly in a browser（fully static, zero dependencies）. The site's layout follows the claude.ai app shell — hairline-bordered collapsible sidebar, floating top-right toolbar, and a documentation column that expands from 48rem up to 72rem on wide screens（84rem in presentation mode）— so the documentation **is** the demo.

- **12 chapters + appendix + full-text mirror**：theme architecture, colors, typography, layout, shape & elevation, motion, components, icons & brand, content styles, page patterns, implementation guide, design playbook
- **Interactive**：click swatches to copy HEX, replay animations, live-rendered components（not screenshots）
- **Appearance segmented control** — system／light／dark modes update every token instantly
- **Presentation mode**：▶ fullscreen slides; navigate with ←／→, toggle dark with D
- **Deep-linkable**：`?slide=7&mode=dark`

<img src=".github/assets/components-dark.png" width='100%' height='100%'/>

## What's Inside

| Path | Purpose |
|---|---|
| [`index.html`](index.html) | The documentation site（single file, GitHub Pages ready） |
| [`tokens.css`](tokens.css) | App tokens：primitives + semantic layer + keyframes + component classes |
| [`tokens-marketing.css`](tokens-marketing.css) | Marketing tokens：swatches, 8 section themes, fluid type & spacing, buttons, segmented selector, cards, filters, prompt/menu, docs shell |
| [`tokens.json`](tokens.json) | Machine-readable tokens（HSL + HEX）for Style Dictionary／Figma Tokens |
| [`docs/`](docs/INDEX.md) | Per-chapter Markdown — the source of truth（in Traditional Chinese）, including the new UI design playbook |
| [`DESIGN_SYSTEM.md`](DESIGN_SYSTEM.md) | The full spec as a single file, readable right on GitHub |
| [`assets/`](assets/) | SVGs：spark, wordmark, 28 app icons, 86 marketing assets（each with a manifest） |
| [`raw/`](raw/) | Evidence：original production CSS and live-captured token dumps |
| [`CLAUDE.md`](CLAUDE.md) | Maintenance guide（sync rules between docs／site／tokens） |

## Quick Start

```html
<html data-theme="claude" data-mode="auto">
  <head><link rel="stylesheet" href="tokens.css"></head>
  <!-- body automatically gets the ivory background & font stack -->
</html>
```

Colors are stored as HSL triplets, so any token takes an alpha：

```css
background: hsl(var(--bg-100));
border-color: hsl(var(--border-200) / 30%);   /* the signature hairline */
```

For marketing-style pages, add `tokens-marketing.css` and theme whole sections：

```html
<section class="u-theme-dark-2">…</section>
```

For UI that is not already listed as a component, start from [docs/12-design-playbook.md](docs/12-design-playbook.md)：choose the surface（App／Marketing／Docs／Hybrid）, pick a layout skeleton, then derive anatomy, states, interaction and accessibility from the token rules.

## 10 Keys to the Claude Look

1. The background is warm ivory（`#faf9f5`／`#f8f8f6`）— never pure white; cards are white
2. AI content and headings use **serif**（Anthropic Serif）; UI uses sans
3. Borders are **0.5px hairlines** of a dark token at 15–30% alpha — almost never solid gray
4. Dark mode **reduces font weights**（bold 600 → 530）, not just colors
5. Shadows are feather-light（black at 4–8%）; featured cards glow with a colored diffuse shadow
6. Buttons press down with `scale(0.985)`; everything transitions in 100–200ms
7. Blue is for interaction（focus ring, selection, links）; clay orange（`#d97757`）is for brand & CTAs
8. Radius hierarchy：buttons 8–9.6px → containers 16px → cards 24px
9. Code blocks are always a One Dark island — even in light mode
10. Dropdowns／modals enter with `fade + zoom(.95)`; marketing nav uses a clip-path curtain

## How It Was Made

Specs were captured from production sites：logged-in claude.ai on **2026-06-12** via browser automation and **re-verified live on 2026-06-15** (reading the running app's `:root` variables — colors, typography and the rest matched exactly, and the App shell / composer / model-selector components were measured and added), and claude.com via a **2026-06-15 full-site inventory**：3,125 public sitemap URLs inventoried, 1,520 canonical marketing pages + 75 docs pages fetched（1595/1595 HTTP 200）, and the latest `claude-brand.shared.9ce205edd.min.css` parsed. Values are cross-checked against archived production bundles from 2025-11 ~ 2026-03. The documentation site renders the extracted tokens and component patterns live — if the tokens were wrong, the site would look wrong.

## License：MIT

The documentation and CSS implementation are [MIT licensed](LICENSE).

> Claude, the Claude logo, wordmark and fonts referenced in `assets/` remain the property of **Anthropic PBC** — included for research and reference purposes only, and are **not** covered by the MIT license.
