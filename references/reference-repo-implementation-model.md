# Reference Repo Implementation Model

这份把 `ancher-site-atlas` 这个 ref repo 抽象成可复制的建站模型。以后 `@webpage`
需要“直接实现整个代码和网页”时，用这份作为默认实现蓝图。

## 1. 技术栈模型

这个 repo 的核心技术栈是：

```text
Static HTML + CSS + Vanilla JS + JSON registries + Python stdlib build script + Vercel static hosting
```

具体组成：

| 层 | 技术 | 作用 |
|---|---|---|
| 页面 | HTML | `site/*.html`、`directions/<id>/index.html`、`modules/**/*.html` 都是静态 HTML。 |
| 样式 | CSS variables + plain CSS | `site/css/atlas.css` 定义 gallery shell tokens；direction 页面各自内联 tokens。 |
| 交互 | Vanilla JS | `site/js/*.js` 负责 fetch registry、渲染 cards、iframe viewer、copy prompt、tabs、labs。 |
| 数据 | JSON registries | `registry.json`、`modules_registry.json`、`skins.json`、`gsap_registry.json` 是网页读取的数据源。 |
| 生成 | Python 3 stdlib | `scripts/build_registry.py` 扫 `directions/*/DESIGN.md`，生成/校验 registry，无第三方依赖。 |
| 动效实验 | Vendored GSAP | 仅 GSAP lab 使用 `vendor/gsap/`，主 direction 页面仍坚持单文件/低依赖。 |
| 本地运行 | `python3 -m http.server` | 从 repo root 起服务，让 `site/` 能 fetch JSON 和相对路径资源。 |
| 部署 | Vercel static | `vercel.json` 做 `/` -> `/site/` redirect，并给 `.md`/`.json` 设置 utf-8 charset。 |

没有 `package.json`、Vite、Tailwind、React 或 npm build step。这个选择本身就是模型的一部分：
可复制、可检查、可静态部署、低维护成本。

## 2. 实现原理模型

核心实现原理：

```text
Source specs -> Registry -> Static gallery -> Viewer shell -> Proof artifacts
```

1. **Source specs**
   每个 direction 是一个独立文件夹，至少包含：

   ```text
   directions/<id>/
     DESIGN.md
     prompt.md
     index.html
     sample.png
     portrait.{jpg,webp} optional
   ```

2. **Registry**
   `scripts/build_registry.py` 读取 `DESIGN.md` frontmatter，提取 `id`、中英文名、
   lineage、best_for、description、colors swatches 和 artifact paths，写入
   `site/registry.json`。脚本同时校验必需文件，缺失就 fail。

3. **Static gallery**
   `site/index.html` 提供 shell 和 `<template>`，`site/js/atlas.js` fetch
   `registry.json` 后渲染 cards。UI 不手写重复 cards，所有 card 来自 registry。

4. **Viewer shell**
   Gallery 打开 `site/view.html?id=<id>`，用 iframe 装载真实 direction 页面。viewer 提供
   prev/next、copy prompt、DESIGN.md、原始页和 component inspector。Direction 页面保持 pristine。

5. **Proof artifacts**
   `sample.png` 是 `index.html` 自己的截图。截图不是装饰，而是 eval proof：
   如果截图不对，说明 spec/prompt/page 有问题。

## 3. 设计模型

设计不是单个页面主题，而是一个可组合系统：

```text
设计多样性 = direction visual language × module structure × content angle × motion/image treatment
```

在复制时保留这些层：

- **Direction**：视觉语言、tokens、字体、颜色、动效语气、禁忌项。
- **Module**：结构和信息组织，如 hero、story、work、metrics、contact。
- **Content angle**：具体 persona、叙事角度、可信证据。
- **Motion/Image treatment**：文字动效、图像处理、GSAP demo 或等价实验层。
- **Browse shell**：gallery、viewer、labs 是 atlas 的操作界面。

设计原则：

- 每个 direction 看起来像“被设计过”，不是通用模板。
- Direction 承载风格，module 承载结构。
- Gallery shell 保持安静中性，避免抢 direction 的风格。
- Viewer/inspector 是外壳，不污染 generated artifact。
- 响应式和可访问性是 contract，不是加分项。

## 4. Design Token 模型

这个 repo 有三层 token。

### 4.1 Direction tokens

每个 `directions/<id>/DESIGN.md` 的 YAML frontmatter 是 direction token 源：

```yaml
id:
name_cn:
name_en:
lineage:
best_for:
accent:
description: >
colors:
  canvas:
  surface:
  ink:
  ink-muted:
  ink-subtle:
  accent:
typography:
  display-xl:
  display-md:
  body:
spacing:
rounded:
motion:
fonts_cdn:
avoid:
reference_vocabulary:
```

复制模型时，保留两种字段：

- **约束字段**：colors、typography、spacing、rounded、motion。它们设定质量地板。
- **纬度字段**：description、avoid、reference_vocabulary、best_for。它们给模型创造空间。

### 4.2 Gallery shell tokens

`site/css/atlas.css` 用全局 CSS vars 定义浏览站外壳：

```css
:root {
  --bg:
  --surface:
  --ink:
  --ink-muted:
  --ink-subtle:
  --line:
  --line-strong:
  --accent:
  --radius:
  --maxw:
  --font:
  --mono:
}
```

这个 shell token 不等于 direction token。Shell 要负责稳定浏览体验，direction 负责个性。

### 4.3 Skin contract

`site/skins.json` 把 direction tokens 映射成共享 module token contract：

```text
--bg
--surface
--surface-2
--ink
--ink-muted
--ink-subtle
--accent
--on-accent
--hairline
--border-strong
--radius-sm
--radius-md
--radius-lg
--font-display
--font-body
--shadow
--shadow-hard
```

这让同一批 modules 可以 live re-skin，而不是生成 `direction × module` 的大量静态页。

## 5. 远程仓库模型

当前 ref repo：

```text
GitHub: streamify-one/ancher-site-atlas
URL: https://github.com/streamify-one/ancher-site-atlas
Visibility: private
Default branch: main
```

复制时建议：

```text
<org>/<project>-site-atlas
main branch
manual deploy snapshots
no generated dependency folders committed
```

如果目标项目是私有知识库，不要直接部署整个 wiki。要像这个 repo 一样，把可公开/可内部浏览的静态站单独成 repo。

`@webpage` 可以自己命名并创建远程仓库。默认命名规则：

```text
<domain>-atlas
<product>-style-lab
<subject>-reference-system
<thing>-catalogue
```

命名要求：

- 使用 kebab-case。
- 简短、描述性、稳定。
- 优先表达长期主题，不表达临时阶段。
- 不使用日期、连续编号、`test`、`demo-final`、`new-repo` 这类含糊名称。
- 如果用户指定 repo 名，以用户指定为准；否则 agent 自行选择最合适的名字。

默认创建流程：

```bash
gh auth status
gh api user --jq '.login'
gh repo view <owner>/<repo> --json nameWithOwner,url,isPrivate,defaultBranchRef 2>/dev/null || true
git init -b main
git add .
git commit -m "Initial import"
gh repo create <owner>/<repo> --private --source=. --remote=origin --push
```

创建前检查：

- 当前有效 GitHub 账号是不是用户要用的个人账号。
- 目标 repo 名是否清晰、稳定、未被占用。
- 是否默认 private；只有用户明确要求 public 才公开。
- 是否有超过 GitHub 100MB 限制的单文件。
- `.gitignore` 是否排除了 `node_modules/`、`.vercel/`、`.claude/`、本地缓存等。

## 6. 部署模型

`vercel.json` 的复制核心：

```json
{
  "redirects": [
    { "source": "/", "destination": "/site/", "permanent": false }
  ],
  "headers": [
    {
      "source": "/(.*).md",
      "headers": [
        { "key": "Content-Type", "value": "text/markdown; charset=utf-8" }
      ]
    },
    {
      "source": "/(.*).json",
      "headers": [
        { "key": "Content-Type", "value": "application/json; charset=utf-8" }
      ]
    }
  ]
}
```

本地运行：

```bash
python3 -m http.server 8799
```

打开：

```text
http://localhost:8799/site/
```

部署：

```bash
vercel deploy . --prod --yes
```

部署原则：

- 从 repo root 部署，不只部署 `site/`，因为页面会引用 `../directions`、`../assets`、`../vendor`。
- `.md` 和 `.json` 必须带 utf-8 charset，否则浏览器直接打开可能 mojibake。
- GitHub 自动部署不是必需项；手动 snapshot deploy 更符合内部 atlas 的节奏。

## 7. 可复制文件骨架

新项目默认生成：

```text
README.md
AGENTS.md
docs/
  CONTEXT.md
  DECISIONS.md
  COVERAGE.md
agents/
  README.md
  research.md
directions/
  <id>/
    DESIGN.md
    prompt.md
    index.html
    sample.png
modules/
  README.md
site/
  index.html
  view.html
  modules.html
  css/
    atlas.css
  js/
    atlas.js
    view.js
    modules.js
  registry.json
  modules_registry.json
  skins.json
assets/
  MANIFEST.md
scripts/
  build_registry.py
vercel.json
```

按目标领域可以改名，例如 `directions/` 变成 `patterns/`、`styles/`、`templates/`，
但 contract 要保持稳定。

## 8. @webpage 实现步骤

当用户要求“直接实现整个代码和网页”时：

1. **确认目标域**：这次 atlas/catalogue 要收集什么。
2. **生成 skeleton**：创建上面的文件结构。
3. **写 seed entries**：至少 3 个高质量样例，每个有 spec、prompt、index、sample 占位或截图计划。
4. **写 build script**：从 spec frontmatter 生成 registry，并校验 artifacts。
5. **写 gallery shell**：静态 HTML + CSS vars + Vanilla JS fetch registry 渲染 cards。
6. **写 viewer shell**：iframe live page，支持 back、prev/next、copy prompt、raw artifact。
7. **写 tokens**：global shell tokens、entry tokens、skin contract。
8. **写 docs**：CONTEXT、DECISIONS、COVERAGE 说明为什么这样做。
9. **写 deploy config**：`vercel.json` + local run + deploy command。
10. **建远程仓库并推送**：自行选择清晰 repo 名，用当前 `gh` 登录账号新建 private GitHub repo，推送 `main`。
11. **验证**：跑 build script，启动本地 server，打开 `/site/` 检查页面。

## 9. 质量线

复制出来的系统必须满足：

- 不依赖重框架也能跑。
- 新增条目只需要加文件夹和 spec，然后跑 registry。
- 所有 UI card 来自 registry，不手写重复列表。
- 每个条目有可复制 prompt/spec 和 live artifact。
- 有 proof：截图、render check 或等价证据。
- Tokens 清晰，不散落成不可控 CSS。
- 远程仓库和部署方式写在 README/CONTEXT 里。
- Agent 可以自己命名、创建 GitHub repo 并推送；最终必须回报 URL、visibility、branch 和 commit。
- 用户下一次只需要 `@webpage` 就能继续扩展或重建网页实现。
