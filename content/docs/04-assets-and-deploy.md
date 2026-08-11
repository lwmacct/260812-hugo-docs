---
title: '04 · 资源与部署'
description: '处理 CSS、构建产物，并把站点发布到静态托管平台。'
weight: 4
type: docs
---

## Hugo Pipes

本项目的 CSS 位于 `assets/css/main.css`，由 `layouts/partials/head.html` 读取并处理：

```go-html-template
{{ $style := resources.Get "css/main.css" | minify | fingerprint }}
<link rel="stylesheet" href="{{ $style.RelPermalink }}">
```

`minify` 压缩内容，`fingerprint` 为文件名加入内容哈希。浏览器因此可以放心缓存旧版本，CSS 改动也会生成新 URL。

## 生产构建

先设置真实域名，再构建：

```bash
hugo --gc --minify --baseURL https://example.com/
```

产物在 `public/`。可以先用静态服务器检查：

```bash
python3 -m http.server 8080 --directory public
```

## GitHub Pages

本仓库使用 GitHub Actions 构建 Hugo，再通过官方 Pages artifact 完成发布。继续阅读 [GitHub Pages](/docs/05-github-pages/)，了解仓库设置、workflow 权限、项目站点路径和 CI 排错方法。

## 部署检查清单

- 生产构建没有使用 `--buildDrafts`。
- `baseURL` 与实际站点地址一致。
- 页面链接使用 `.RelPermalink`，不会把本地路径写死。
- 图片、字体等资源在 `public/` 中存在。
- 在移动端和无 JavaScript 环境下，文章仍然可读。
