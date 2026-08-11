---
title: '05 · GitHub Pages'
description: '使用 GitHub Actions 自动构建 Hugo，并发布到 GitHub Pages。'
weight: 5
type: docs
---

GitHub Pages 可以直接托管 Hugo 生成的静态文件。本仓库使用 GitHub Actions 在每次推送到 `main` 后构建站点，再把 `public/` 作为 Pages artifact 发布；构建产物不提交到 Git。

完整 workflow 位于 [`.github/workflows/hugo.yaml`](https://github.com/lwmacct/260812-hugo-docs/blob/main/.github/workflows/hugo.yaml)。本章以 [Hugo 官方 GitHub Pages 指南](https://gohugo.io/host-and-deploy/host-on-github-pages/) 为基础，并针对当前仓库做了精简。

## Pages 站点类型

GitHub Pages 有三种常见类型：

- 项目站点：地址通常是 `https://<owner>.github.io/<repository>/`。
- 用户站点：仓库名必须是 `<owner>.github.io`，地址位于域名根路径。
- 组织站点：规则与用户站点类似，由组织账号托管。

本仓库是项目站点，发布地址是：

```text
https://lwmacct.github.io/260812-hugo-docs/
```

项目站点多了一层仓库路径。不能把生产 `baseURL` 固定为 `/`，否则 CSS、RSS 和页面链接可能指向错误位置。本项目由 `actions/configure-pages` 计算真实地址，并在构建时传给 Hugo：

```yaml
- name: Setup Pages
  id: pages
  uses: actions/configure-pages@v6

- name: Build site
  run: |
    hugo \
      --gc \
      --minify \
      --baseURL "${{ steps.pages.outputs.base_url }}/"
```

## 首次启用 Pages

1. 打开 GitHub 仓库的 **Settings**。
2. 在左侧选择 **Pages**。
3. 在 **Build and deployment** 中把 **Source** 设置为 **GitHub Actions**。
4. 推送 `.github/workflows/hugo.yaml` 到默认分支。
5. 打开仓库的 **Actions** 页面，等待 build 和 deploy 两个任务完成。

不要提交 `public/`。Hugo 会在每次 CI 中重新创建它，本仓库已通过 `.gitignore` 忽略该目录。

{{< notice type="tip" >}}
Pages 的 Source 设置修改后立即生效，不需要额外保存。仓库当前已经使用 GitHub Actions 作为发布源。
{{< /notice >}}

## Workflow 的关键部分

触发条件：

```yaml
on:
  push:
    branches:
      - main
  workflow_dispatch:
```

推送 `main` 会自动部署，`workflow_dispatch` 则允许在 Actions 页面手动点击 **Run workflow**。

最小权限：

```yaml
permissions:
  contents: read
  pages: write
  id-token: write
```

- `contents: read` 用于检出源码。
- `pages: write` 允许发布 Pages artifact。
- `id-token: write` 允许部署任务使用 OIDC 完成身份验证。

workflow 分为两个 job：`build` 安装指定版本的 Hugo、恢复缓存并生成 `public/`；`deploy` 只接收构建产物并发布到 `github-pages` 环境。两个阶段分开后，部署任务不会获得修改仓库内容的权限。

`concurrency.group: pages` 保证 Pages 发布按顺序执行。`cancel-in-progress: false` 会让已开始的部署完成，避免新推送中途取消正在发布的版本。

## Hugo 缓存

workflow 把缓存放在 runner 临时目录：

```yaml
--cacheDir "${RUNNER_TEMP}/hugo_cache"
```

`hugo.toml` 同时把图片缓存放到 `cacheDir` 下：

```toml
[caches]
  [caches.images]
    dir = ':cacheDir/images'
```

这样 `actions/cache` 可以跨构建复用 Hugo 处理过的资源，而不会把缓存文件写进仓库。

## 手动运行和观察 CI

可以在 GitHub 网页中打开 **Actions**，选择 **Deploy Hugo site to Pages**，然后点击 **Run workflow**。使用 GitHub CLI 时，对应命令是：

```bash
gh workflow run hugo.yaml
gh run list --workflow hugo.yaml --limit 5
gh run watch RUN_ID --exit-status
```

查看失败日志：

```bash
gh run view RUN_ID --log-failed
```

先从 `gh run list` 的输出中取得数字形式的 `RUN_ID`。构建成功后，deploy job 的输出和仓库 **Settings > Pages** 都会显示最终站点地址。

## 常见问题

### 页面成功但 CSS 404

通常是项目站点的仓库路径丢失。确认构建命令使用了 `steps.pages.outputs.base_url`，模板链接使用 `.RelPermalink` 或 `relURL`，不要写死域名根路径。

### deploy 提示权限不足

确认 workflow 声明了 `pages: write` 和 `id-token: write`，并确认 **Settings > Pages > Source** 是 **GitHub Actions**。

### artifact 中没有网站

确认 Hugo 构建成功、输出目录仍为 `public/`，且 `actions/upload-pages-artifact` 的 `path` 指向 `./public`。

### 新文章没有发布

生产构建不会使用 `--buildDrafts` 或 `--buildFuture`。检查页面是否设置了 `draft: true`，以及 `date` 是否晚于 CI 的当前时间。

### 需要 SCSS、Go Module 或 npm

本项目目前只使用普通 CSS，因此 workflow 只安装 Hugo。引入 SCSS、Hugo Module 或前端构建工具后，应在 build job 中增加相应的 Dart Sass、Go 或 Node.js 安装步骤，并锁定工具版本。
