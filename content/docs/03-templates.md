---
title: '03 · 模板系统'
description: '从 baseof 到 shortcode，掌握 Hugo 的模板查找与复用。'
weight: 3
type: docs
---

## 模板查找顺序

`layouts/_default/baseof.html` 是本项目的基础模板。它定义 HTML 外壳，并通过 `block` 把页面内容交给具体模板：

```go-html-template
{{ define "main" }}
  <main>{{ .Content }}</main>
{{ end }}
```

`layouts/index.html` 负责首页，`layouts/_default/list.html` 负责分区列表，`layouts/_default/single.html` 负责单页。Hugo 会根据页面类型自动选择匹配的模板。

## 数据与导航

站点菜单在 `hugo.toml` 的 `[menu.main]` 中配置，模板通过 `.Site.Menus.main` 遍历：

```go-html-template
{{ range site.Menus.main }}
  <a href="{{ .URL | relURL }}">{{ .Name }}</a>
{{ end }}
```

页面 front matter 的字段通过 `.Params` 读取，例如 `{{ .Params.description }}`。站点参数则来自 `[params]`，可以用 `.Site.Params.author` 访问。

## Shortcode

`layouts/shortcodes/notice.html` 提供一个可复用提示框。在 Markdown 中写：

```text
{{</* notice type="tip" */>}}
先运行 `task dev`，再修改内容观察热更新。
{{</* /notice */>}}
```

Shortcode 适合表达 Markdown 不方便描述的交互或布局。不要把整篇文章都塞进 shortcode；能用普通 Markdown 表达的内容应保持简单。

## 调试模板

开发时可以临时打印对象：

```go-html-template
<pre>{{ debug.Dump .Params }}</pre>
```

确认数据结构后及时删除调试输出。构建失败时，Hugo 会在终端给出模板文件和行号，先从最靠近报错位置的模板开始排查。
