# 碳基漫游 AI 博客

这是面向中国大陆小白的 ChatGPT 订阅教程博客，使用 Hugo Extended、Liquid Stack 和 GitHub Pages 发布。

## 本地运行

在仓库根目录执行：

```bash
hugo server -D
```

正式构建：

```bash
hugo --minify --cleanDestinationDir --ignoreCache
```

## 目录说明

- `content/post/`：13 篇教程文章；
- `content/page/`：关于本站、启动台、仪表盘等页面；
- `layouts/`、`assets/`：Liquid Stack 的站点扩展与样式；
- `data/launchpad/`：教程启动台数据；
- `static/img/`：站点图片和占位素材；
- `themes/stack/`：Hugo Theme Stack v4.0.3 核心主题。

Liquid Stack 由 [Jingyuan-Zheng](https://github.com/Jingyuan-Zheng) 开源，本仓库保留其署名链接。评论功能暂不启用；Sveltia CMS 暂不接入，文章继续使用 Markdown 管理。

## 发布

向 `main` 分支 Push 后，GitHub Actions 会使用 Hugo Extended 构建并发布到 GitHub Pages。网站地址：

https://xizhiyun1995-netizen.github.io/
