---
title: "从零搭建 HaoNest：个人博客完整搭建与部署记录"
date: 2024-01-01
categories: ["建站"]
tags: ["Hugo", "博客", "GitHub Pages", "PaperMod"]
---

本文记录 HaoNest 个人站点从零到上线的完整过程，包含环境搭建、主题配置、功能开发、自动部署等所有步骤。

## 站点介绍

HaoNest 是一个集三大功能于一体的个人站点：

- **博客**：Markdown 写作，支持分类、标签、搜索、评论
- **导航**：收藏常用网站，按类别整理
- **工具**：内置多种开发工具，无需安装，即开即用

技术选型如下：

| 模块 | 选择 |
|------|------|
| 静态站点生成器 | Hugo |
| 主题 | PaperMod |
| 托管 | GitHub Pages |
| 评论 | Giscus |
| 部署 | GitHub Actions |

---

## 一、环境准备

### 1.1 安装必要工具

需要提前安装以下工具：

- **Git**：版本管理
- **GitHub CLI**：命令行操作 GitHub
- **Node.js**：部分工具依赖
- **Hugo Extended**：站点生成器

安装 Hugo（Windows）：

```bash
winget install Hugo.Hugo.Extended
```

验证安装：

```bash
hugo version
```

输出类似 `hugo v0.159.1+extended` 即为成功。

---

## 二、创建 Hugo 项目

### 2.1 初始化项目

```bash
hugo new site . --force
git init
```

### 2.2 安装 PaperMod 主题

```bash
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

### 2.3 配置 hugo.toml

删除默认的 `hugo.toml`，新建完整配置：

```toml
baseURL = 'https://lhao4.github.io/'
languageCode = 'zh-cn'
title = 'HaoNest'
theme = 'PaperMod'
paginate = 10
enableRobotsTXT = true

[params]
  env = 'production'
  author = 'Hao'
  defaultTheme = 'auto'
  ShowReadingTime = true
  ShowPostNavLinks = true
  ShowBreadCrumbs = true
  ShowCodeCopyButtons = true
  ShowWordCount = true
  ShowRssButtonInSectionTermList = true
  UseHugoToc = true
  showtoc = true
  tocopen = false
  comments = true

[[params.socialIcons]]
  name = 'github'
  url = 'https://github.com/lhao4'

[[params.socialIcons]]
  name = 'rss'
  url = '/index.xml'

[[menu.main]]
  identifier = 'posts'
  name = '博客'
  url = '/posts/'
  weight = 10

[[menu.main]]
  identifier = 'nav'
  name = '导航'
  url = '/nav/'
  weight = 20

[[menu.main]]
  identifier = 'tools'
  name = '工具'
  url = '/tools/'
  weight = 30

[[menu.main]]
  identifier = 'categories'
  name = '分类'
  url = '/categories/'
  weight = 40

[[menu.main]]
  identifier = 'tags'
  name = '标签'
  url = '/tags/'
  weight = 50

[[menu.main]]
  identifier = 'favorites'
  name = '收藏'
  url = '/favorites/'
  weight = 60

[[menu.main]]
  identifier = 'search'
  name = '搜索'
  url = '/search/'
  weight = 70

[outputs]
  home = ['HTML', 'RSS', 'JSON']

[markup]
  [markup.goldmark]
    [markup.goldmark.renderer]
      unsafe = true
  [markup.highlight]
    noClasses = false
    style = 'github-dark'
```

### 2.4 创建搜索页

PaperMod 需要手动创建搜索页：

```bash
# content/search.md
---
title: "搜索"
layout: "search"
---
```

### 2.5 本地预览

```bash
hugo server -D
```

打开 `http://localhost:1313` 即可预览。

---

## 三、首页定制

### 3.1 数据统计

覆盖 PaperMod 的默认首页，新建 `layouts/index.html`，在顶部展示文章数、分类数、标签数、工具数统计，下方展示文章列表。

Hugo 提供以下模板变量获取统计数据：

```go
{{- $posts := len (where site.RegularPages "Section" "posts") }}
{{- $tags := len site.Taxonomies.tags }}
{{- $categories := len site.Taxonomies.categories }}
{{- $tools := len (where site.RegularPages "Section" "tools") }}
```

### 3.2 博客页分类过滤

在 `layouts/posts/list.html` 中，顶部渲染分类按钮，每篇文章的 `<article>` 标签上附加 `data-cats` 属性，通过 JavaScript 实现点击分类后实时过滤文章，无需跳转页面。

---

## 四、导航页

导航页的网站数据集中存放在 `data/nav.yaml`，通过 Hugo 模板循环渲染，新增或删除网站只需修改 YAML 文件：

```yaml
categories:
  - name: 开发工具
    icon: "🛠️"
    sites:
      - name: GitHub
        url: https://github.com
        desc: 全球最大代码托管平台
```

在 `layouts/_default/nav.html` 中使用 `range .Site.Data.nav.categories` 遍历数据渲染卡片。

导航页和工具页需要全宽展示，通过 JavaScript 给 `body` 添加自定义 class，再用 CSS 覆盖 PaperMod 的宽度限制：

```css
body.nav-page .main,
body.tools-page .main {
  max-width: 100%;
  padding: 0 2rem;
}
```

---

## 五、在线工具

每个工具存放在 `content/tools/{工具名}/index.md`，使用统一的 `layouts/tools/single.html` 作为页面框架，工具逻辑使用纯 HTML + JavaScript 实现，可直接引入 CDN 库。

目前已实现的工具：

| 类别 | 工具 |
|------|------|
| 编码/加密 | 进制转换、URL 编解码、MD5/SHA 加密、UUID 生成 |
| 格式化 | JSON、XML、SQL 格式化 |
| 开发调试 | 正则测试、JWT 解析 |
| 实用工具 | 时间戳转换、IP 查询 |

---

## 六、文章阅读体验优化

### 6.1 阅读进度条

在 `layouts/partials/extend_head.html` 中添加 CSS，在 `layouts/partials/extend_footer.html` 中添加 JS，监听 `scroll` 事件动态更新顶部进度条宽度：

```javascript
window.addEventListener('scroll', () => {
  const scrollTop = window.scrollY;
  const docHeight = document.documentElement.scrollHeight - window.innerHeight;
  bar.style.width = (scrollTop / docHeight * 100) + '%';
}, { passive: true });
```

### 6.2 侧边固定目录

PaperMod 自带目录（TOC），通过 JavaScript 将其克隆到固定定位的侧边栏，并在滚动时高亮当前所在章节。屏幕宽度小于 1200px 时自动隐藏，不影响移动端体验。

---

## 七、收藏功能

收藏功能基于浏览器 `localStorage` 实现，无需后端：

- 文章页标题旁显示收藏按钮（☆ / ★）
- 点击后将文章标题、URL、收藏日期存入 `localStorage`
- `/favorites/` 页面读取并展示所有收藏的文章，支持单条删除和清空

数据结构：

```json
[
  {
    "title": "文章标题",
    "url": "/posts/hello-world/",
    "date": "2026/3/27"
  }
]
```

---

## 八、评论系统（Giscus）

Giscus 基于 GitHub Discussions，评论数据存储在仓库的 Discussions 中，无需额外数据库。

### 8.1 准备工作

1. 创建公开 GitHub 仓库
2. 在仓库 Settings → Features 中启用 **Discussions**
3. 访问 [github.com/apps/giscus](https://github.com/apps/giscus) 安装 Giscus App，授权到对应仓库
4. 访问 [giscus.app](https://giscus.app/zh-CN) 生成配置代码

### 8.2 接入 Hugo

在 `layouts/partials/comments.html` 中写入 Giscus 脚本，`hugo.toml` 中设置 `comments = true` 即可在所有文章底部显示评论区。

主题设置为 `preferred_color_scheme`，自动跟随站点亮/暗模式切换。

---

## 九、自动部署

### 9.1 创建 GitHub Actions 工作流

新建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy Hugo to GitHub Pages

on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: recursive
      - uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: '0.159.1'
          extended: true
      - run: hugo --minify
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - uses: actions/deploy-pages@v4
```

### 9.2 配置 GitHub Pages

在仓库 **Settings → Pages** 中，将 Source 改为 **GitHub Actions**。

### 9.3 推送上线

```bash
git add .
git commit -m "your commit message"
git push
```

推送后 GitHub Actions 自动触发构建，约 1-2 分钟后站点更新。

---

## 十、目录结构总览

```
blog/
├── .github/workflows/deploy.yml   # 自动部署
├── assets/css/extended/custom.css # 自定义样式
├── content/
│   ├── posts/                     # 博客文章
│   ├── tools/                     # 在线工具
│   ├── nav.md                     # 导航页
│   ├── search.md                  # 搜索页
│   └── favorites.md               # 收藏页
├── data/nav.yaml                  # 导航数据
├── layouts/
│   ├── index.html                 # 首页
│   ├── _default/
│   │   ├── single.html            # 文章页（含收藏按钮）
│   │   ├── nav.html               # 导航页布局
│   │   ├── tools.html             # 工具首页布局
│   │   └── favorites.html         # 收藏页布局
│   ├── posts/list.html            # 博客列表页
│   ├── tools/single.html          # 工具页布局
│   └── partials/
│       ├── comments.html          # Giscus 评论
│       ├── extend_head.html       # 进度条样式
│       └── extend_footer.html     # 进度条+目录 JS
├── themes/PaperMod/               # 主题（submodule）
└── hugo.toml                      # 站点配置
```

---

以上就是 HaoNest 从零到上线的完整流程。后续会持续更新笔记和工具，欢迎访问 [lhao4.github.io](https://lhao4.github.io)。
