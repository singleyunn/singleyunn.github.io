# xizhiyun · Liquid Stack 模板

这是一个使用 [Liquid Stack](https://github.com/Jingyuan-Zheng/Liquid-Stack) 主题的 Hugo 个人博客模板，部署到 GitHub Pages。

当前公开站点沿用 Liquid Stack 的模块结构，但已替换为“xizhiyun”的文章、资源、照片墙和仪表盘内容；原来的 ChatGPT 订阅教程已移出公开内容，教程源文件和此前提交仍保留在本地备份 / Git 历史中。

## 已接入模块

- 双语内容结构：英文首页与 `/zh/` 中文首页；
- Liquid Glass 风格个人主页；
- 在线工具与网站资源导航；
- 可拖动照片墙；
- 内容仪表盘；
- 友链页面与示例申请流程；
- Sveltia CMS 文件和配置；
- 搜索、归档、分类、标签云和站点地图。

## CMS 状态

`/admin/` 已部署 Sveltia CMS 管理入口，配置使用 GitHub backend，目标仓库为本仓库的 `main` 分支。分类和标签词表位于 `data/taxonomies/`，供 CMS 的关联字段使用。

首次使用需要生成并输入一个对该仓库有写入权限的 GitHub Personal Access Token；令牌只保存在当前浏览器的本地存储，不进入仓库。保存会直接提交到 `main`，随后由 GitHub Actions 构建并发布 GitHub Pages。CMS 前端从 `unpkg.com` 加载，普通公开页面不会加载 CMS 编辑器。

首次登录、编辑和提交测试仍需由仓库所有者在自己的 GitHub 账号下完成；在此之前不要把 `/admin/` 当作无认证的公开投稿入口。

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

- `content/post/`：加入或替换自己的文章；
- `content/page/`：替换关于、链接和其他页面；
- `data/launchpad/`：替换资源导航条目；
- `data/photo-wall/` 与 `static/img/gallery/`：替换照片墙；
- `static/img/github.png`：当前个人头像；
- `hugo.yaml`：站点名称、简介、语言和服务配置。

## 发布

向 `main` 分支 Push 后，GitHub Actions 使用 Hugo Extended 构建并发布到：

https://xizhiyun1995-netizen.github.io/
