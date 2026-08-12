# SingleYunn Blog（SYB）

SingleYunn 的独立个人博客项目，使用 Hugo Extended 与 Liquid Stack 构建，通过 GitHub Actions 发布到 GitHub Pages。

- 本地项目：`D:\syb`
- 公开站点：<https://singleyunn.github.io/>
- 源码仓库：<https://github.com/singleyunn/singleyunn.github.io>
- 默认分支：`main`

## 项目状态

当前站点提供英文首页与 `/zh/` 中文首页，已启用文章、资源导航、相册、搜索、归档、分类、标签、站点地图和内容管理入口。评论功能保留代码但默认关闭。

本项目与其他项目完全隔离：目录、Git 历史、构建产物和维护文档均只属于 `D:\syb`。

## 主要目录

| 路径 | 用途 |
| --- | --- |
| `content/` | 双语文章与页面 |
| `data/` | 资源导航、相册、分类与其他结构化数据 |
| `static/` | 图片和其他原样发布的静态文件 |
| `assets/` | Hugo 处理的资源与 CMS 基础配置 |
| `layouts/` | 项目自定义模板和局部布局 |
| `themes/stack/` | Liquid Stack 主题 |
| `.github/workflows/deploy.yml` | GitHub Pages 构建与部署流程 |
| `hugo.yaml` | Hugo 站点配置 |
| `agent.md` | 本项目的 AI 协作边界与工作流程 |

`public/`、`resources/` 和 `.hugo_build.lock` 是本地生成物，已由 `.gitignore` 排除。

## 本地运行

需要 Hugo Extended。CI 当前固定使用 Hugo `0.164.0`，本地验证宜使用相同版本。

开发预览：

```bash
hugo server -D
```

正式构建：

```bash
hugo --minify --cleanDestinationDir --ignoreCache
```

构建输出位于 `public/`。

## 内容维护

- `content/post/`：文章；
- `content/page/`：关于、链接、隐私等页面；
- `data/launchpad/`：资源导航；
- `data/photo-modules.yaml`：相册合集配置；
- `data/photo-wall/` 与 `static/img/gallery/<module-id>/`：相册数据与图片；
- `static/img/github.png`：站点头像；
- `hugo.yaml`：站点名称、简介、语言、菜单和服务配置。

修改公共信息时，应同步检查英文和中文内容。

## CMS

`/admin/` 提供 Sveltia CMS 管理入口，基础配置位于：

```text
assets/admin/cms-config-base.yml
```

CMS 使用 GitHub backend，目标仓库为 `singleyunn/singleyunn.github.io` 的 `main` 分支。首次使用需要由仓库所有者在自己的浏览器中提供具备适当权限的 GitHub Token。

Token 只能保存在用户自己的浏览器环境中，不得写入仓库、文档、日志或截图。CMS 保存内容会直接提交到 `main`，随后触发 GitHub Actions 部署。

## 发布流程

1. 在本地完成内容或配置修改；
2. 运行正式 Hugo 构建；
3. 检查 `git diff --check`、最终差异和 Git 状态；
4. 创建清晰的本地 Commit；
5. 获得明确授权后 Push 到 `main`；
6. 在 GitHub Actions 中确认 `Deploy to GitHub Pages` 成功；
7. 打开 <https://singleyunn.github.io/> 验证页面、样式、图片和链接。

仅有本地构建成功不代表线上发布成功，GitHub Actions 和公开页面均属于发布验收证据。

## 地址变更检查

用户名、仓库名或域名发生变化时，至少检查：

- `hugo.yaml` 中的 `baseURL` 和社交链接；
- `assets/admin/cms-config-base.yml`；
- About、Links 与 Privacy 双语页面；
- `data/management_links.yaml`；
- 页脚链接与本 README；
- 本地 Git remote；
- GitHub Actions 与 Pages 页面。

## 安全边界

- 不提交 Token、密码、Cookie、恢复码或私钥；
- 未经授权不修改 Pages 设置、Secrets 或发布配置；
- 新增第三方脚本、分析、评论或远程服务前，先评估数据流与隐私影响；
- 详细协作规则见 [`agent.md`](agent.md)。
