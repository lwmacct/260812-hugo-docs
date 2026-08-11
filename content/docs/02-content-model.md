---
title: '02 · 内容模型'
description: '理解 front matter、页面类型、分区和 URL。'
weight: 2
type: docs
---

## Front matter

Markdown 顶部的 YAML/TOML 区块称为 front matter，用来描述页面的结构化数据：

```yaml
---
title: '一篇文章'
date: 2026-08-12
draft: false
description: '搜索摘要和列表页会使用它。'
tags: ['基础']
categories: ['学习']
---
```

本项目的文章模板会把 `description`、`tags` 和 `categories` 输出到列表页与页面元数据中。字段名可以自定义，但模板必须使用同一个名字读取它。

## 分区与页面

`content/docs/02-content-model.md` 的分区是 `docs`，所以它的默认 URL 是 `/docs/02-content-model/`。`content/docs/_index.md` 是分区页，负责介绍整个学习路径。

常用页面集合：

```go-html-template
{{ range site.RegularPages }}
  <a href="{{ .RelPermalink }}">{{ .Title }}</a>
{{ end }}
```

- `.Page` 表示当前页面。
- `.Site`（也可写成 `site`）表示站点级数据。
- `.Pages` 是当前分区的子页面集合。
- `.RegularPages` 排除分区首页等 bundle 页面。

## 页面 Bundle

把图片和 Markdown 放进同一个目录，就能创建 leaf bundle：

```text
content/posts/launch/
├── index.md
└── screenshot.png
```

在模板中用 `.Resources.GetMatch "screenshot.png"` 取得资源。需要直接访问的原始文件放在 `static/`，会被复制到输出目录且不会经过资源处理。

## 草稿与日期

`draft: true` 的页面默认不会出现在生产构建中；`date` 会影响排序。构建前可以检查最终结果：

```bash
hugo --gc --minify
```

`--gc` 清理缓存中不再使用的资源，`--minify` 压缩 HTML、CSS 和 JSON。
