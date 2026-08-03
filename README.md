# 碳基漫游 AI · Liquid Stack 模板

这是基于朋友小景开源的 [Liquid Stack](https://github.com/Jingyuan-Zheng/Liquid-Stack) 整理的 Hugo 个人博客模板，部署到 GitHub Pages。

当前公开站点先保留 Liquid Stack 的完整模块和示例内容，原来的 ChatGPT 订阅教程已移出公开内容；教程源文件和此前提交仍保留在本地备份/ Git 历史中，后续可以逐篇重新加入。

## 已接入模块

- 双语内容结构：英文首页与 `/zh/` 中文首页；
- Liquid Glass 风格个人主页；
- 在线工具与网站资源导航；
- 可拖动照片墙；
- 内容仪表盘；
- 友链页面与示例申请流程；
- Sveltia CMS 文件和配置；
- 搜索、归档、分类、标签云和站点地图。

评论模块代码保留，但评论功能暂不启用；接入真实 Waline 服务前不要填写服务地址。

## 本地运行

```bash
hugo server -D
```

正式构建：

```bash
hugo --minify --cleanDestinationDir --ignoreCache
```

## 后续替换内容

- `content/post/`：替换示例文章；
- `content/page/`：替换关于、链接和其他页面；
- `data/launchpad/`：替换资源导航条目；
- `data/photo-wall/` 与 `static/img/gallery/`：替换照片墙；
- `static/img/avatar.jpg`：当前个人头像；
- `hugo.yaml`：站点名称、简介、语言和服务配置。

## 发布

向 `main` 分支 Push 后，GitHub Actions 使用 Hugo Extended 构建并发布到：

https://xizhiyun1995-netizen.github.io/
