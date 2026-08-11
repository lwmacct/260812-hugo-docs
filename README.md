# Hugo 学习手册

[![Deploy Hugo site to Pages](https://github.com/lwmacct/260812-hugo-docs/actions/workflows/hugo.yaml/badge.svg)](https://github.com/lwmacct/260812-hugo-docs/actions/workflows/hugo.yaml)

这是一个从零搭建的 Hugo 学习项目。它不依赖外部主题，目录、模板和资源都在仓库中，适合边运行边阅读。

## 快速开始

先安装 Hugo 0.158.0 或更高版本（建议使用 extended 版本），然后在仓库根目录运行：

```bash
hugo server --buildDrafts --disableFastRender
```

访问 <http://localhost:1313/>。也可以使用 [Taskfile](./Taskfile.yml)：

```bash
task dev       # 启动开发服务器
task build     # 清理并生成 public/
task check     # 检查配置和内容
```

## 项目结构

```text
archetypes/    新内容的 front matter 模板
assets/        由 Hugo Pipes 处理的 CSS
content/       中文文档与示例文章
layouts/       基础模板、页面模板、partials、shortcodes
hugo.toml      站点配置、菜单和 taxonomy
```

## 阅读顺序

1. [开始使用](content/docs/01-getting-started.md)：安装、启动和目录职责。
2. [内容模型](content/docs/02-content-model.md)：front matter、分区和页面 Bundle。
3. [模板系统](content/docs/03-templates.md)：模板查找、数据和 shortcode。
4. [资源与部署](content/docs/04-assets-and-deploy.md)：Hugo Pipes、生产构建和托管。
5. [GitHub Pages](content/docs/05-github-pages.md)：Actions workflow、Pages 设置和 CI 排错。

## 在线站点

推送到 `main` 后，GitHub Actions 会自动构建并发布到 <https://lwmacct.github.io/260812-hugo-docs/>。首次使用时，需要在仓库 **Settings > Pages** 中把 Source 设置为 **GitHub Actions**。

## 练习建议

使用 `hugo new posts/my-first-post.md` 创建草稿，修改 front matter 后观察列表页变化；接着改动 `layouts/_default/single.html` 或 `assets/css/main.css`，理解内容与呈现的边界。
