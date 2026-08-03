---
title: "rpiv-todo 扩展使用说明"
description: "介绍如何在 Pi 编辑器上方维护跨会话保留的任务面板。"
date: "2026-08-01"
slug: "rpiv-todo"
categories: [AI 工具]
tags: ["Pi","扩展","任务管理"]
image: "/img/article-covers/rpiv-todo.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 更新日期：2026-08-01 ｜ 参考版本：2.3.0 ｜ 许可：MIT

## 工具介绍

rpiv-todo 给 Pi 一个可见的任务清单：`todo` 工具 + `/todos` 命令 + **编辑器上方的实时面板**（`Todos (完成/总数)` 标题、状态图标、进行中任务标签）。长任务（研究→设计→实现）期间随时知道代理在做什么、做完了什么、还排着什么。

- 列表从会话本身重建，**`/reload` 与上下文压缩后依然存活**（无磁盘写入）
- 完成项当前轮次结束后收起，清单清空后面板自动消失
- 行数超预算自动折叠，不吞终端
- 来自 `juicesharp/rpiv-mono` 全家桶（547★，活跃维护），可独立安装；pi.dev 下载量约 29K/月

## 安装

```bash
pi install npm:@juicesharp/rpiv-todo
```

**重启 Pi 会话生效。**

## 卸载

```bash
pi remove npm:@juicesharp/rpiv-todo
```

## 基本使用

重启后先验证：

```text
/todos
```

新会话会提示 "No todos yet"。然后给一个多步任务，如"加一个带测试的仓库层，并用 todo 跟踪"。模型调用 `todo` 后面板出现在输入框上方并随进度更新。

- `ctrl+shift+t`：折叠面板为标题+单行提示，再按一次展开
- `/todos`：随时打印按状态分组的完整清单

## 与 pi-powerline-footer 的共存

**已做静态分析验证，无冲突，可同装**：

- rpiv-todo 使用标准 widget API 注册到 `aboveEditor` 组件容器（`todo-overlay.ts` 第 77 行 `{ placement: "aboveEditor" }`）
- powerline 的 fixed-editor 模式**保留该容器**并纳入自己的合成布局（`index.ts` 第 2432 行定位容器、2484 行 `compositor.renderHidden(fixedWidgetContainerAbove)` 渲染），而非替换它
- 快捷键冲突由 powerline 自带检测与自动替换机制处理

重启后如目视发现挤压或重叠，可用 `ctrl+shift+t` 折叠面板或 `/powerline fixed-editor off` 对照排查。

## 参考

- 本地已装包 README：`%USERPROFILE%\.pi\agent\npm\node_modules\@juicesharp\rpiv-todo\README.md`
- GitHub：https://github.com/juicesharp/rpiv-mono/tree/main/packages/rpiv-todo
