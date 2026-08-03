---
title: 配置 Waline 评论区
description: 保留评论前端，并安全接入自己的 Waline 服务。
date: 2026-07-22
slug: configure-waline-comments
categories: [教程]
tags: [Waline, 评论, 配置]
---

Liquid Stack 已保留 Waline 评论组件；示例地址不会连接到任何真实后端。

## 准备服务端

先按照 Waline 官方文档部署自己的服务端。获得服务地址后，在 `hugo.yaml` 的 `params.comments.waline.serverURL` 中替换示例 URL。

## 启用与关闭

将 `params.comments.enabled` 设为 `true` 可启用评论。单篇文章也可以在前置数据中设为 `comments: false`，例如隐私页、说明页或不希望讨论的公告。

## 发布前测试

本地预览时检查文章底部是否出现评论框；发布后再测试提交、审核和邮件通知。服务器地址、访问令牌和管理员配置应放在自己的部署环境中，不应保留在公开主题示例里。
