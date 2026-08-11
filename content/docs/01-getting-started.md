---
title: '01 · 开始使用'
description: '安装 Hugo，启动开发服务器，并认识一个站点的基本目录。'
weight: 1
type: docs
---

## 安装 Hugo

Hugo 有 extended 版本和 standard 版本。需要处理 Sass/SCSS 时选择 extended；本仓库只使用普通 CSS，因此任一版本都可以。

macOS（Homebrew）：

```bash
brew install hugo
```

Debian/Ubuntu 可以从 [Hugo Releases](https://github.com/gohugoio/hugo/releases) 下载对应架构的压缩包。安装后确认版本：

```bash
hugo version
```

## 启动项目

在仓库根目录执行：

```bash
hugo server --buildDrafts --disableFastRender
```

打开 <http://localhost:1313/>。修改内容或模板后，浏览器会自动刷新。`--buildDrafts` 让 `draft: true` 的页面也参与构建；正式预览时可以去掉它。

也可以使用仓库提供的 Taskfile：

```bash
task dev
```

{{< notice type="tip" >}}
开发阶段保留终端输出。Hugo 的模板错误会包含文件名和行号，通常比浏览器中的空白页面更容易定位问题。
{{< /notice >}}

## 目录职责

```text
.
├── archetypes/       # hugo new 生成内容时使用的前置模板
├── assets/           # 会被 Hugo Pipes 处理的资源
├── content/          # Markdown 内容，目录决定 URL 层级
├── layouts/          # 模板与 shortcode
├── static/           # 原样复制到 public/ 的文件
├── hugo.toml         # 站点配置
└── public/           # hugo 构建产物（不提交）
```

Hugo 的输入是 `content/`、`layouts/` 和资源目录，输出默认是 `public/`。`public/` 已在 `.gitignore` 中忽略。

## 创建第一篇文章

```bash
hugo new posts/my-first-post.md
```

命令会读取 `archetypes/default.md`，生成带有标题、日期和草稿状态的 Markdown 文件。编辑完成后，把 front matter 中的 `draft` 改成 `false`，或者使用 `hugo server --buildDrafts` 预览。
