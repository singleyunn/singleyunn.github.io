---
title: "rpiv-ask-user-question 扩展使用说明"
description: "让模型在重要决策前弹出结构化问卷，减少猜测和返工。"
date: "2026-08-01"
slug: "rpiv-ask-user-question"
categories: [AI 工具]
tags: ["Pi","扩展","交互"]
image: "/img/article-covers/rpiv-ask-user-question.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 更新日期：2026-08-01 ｜ 参考版本：2.3.0 ｜ 许可：MIT

## 工具介绍

rpiv-ask-user-question 给模型一个 `ask_user_question` 工具：当模型面临真实决策时，弹出**终端对话框**（最多 4 问），每问带编号选项 + 自由输入行，你的选择以结构化数据返回给模型。

价值：让模型"问而不是猜"——花 15 秒选择，而不是花 1 小时返工一个错误假设。与「先澄清再动手」的工作原则高度契合。

- 来自 `juicesharp/rpiv-mono` 全家桶（547★，活跃维护，07-31 仍有更新），可独立安装
- 本机 pi.dev 下载量约 33K/月

## 安装

```bash
pi install npm:@juicesharp/rpiv-ask-user-question
```

**重启 Pi 会话生效。**

## 卸载

```bash
pi remove npm:@juicesharp/rpiv-ask-user-question
```

## 基本使用

零配置，重启后工具即生效。模型判断需要决策时会自动调用，例如：

```text
给 API 客户端加缓存。
```

模型不再擅自选策略，而是弹出对话框：↑/↓ 选择、Enter 确认、`n` 附加注释、Tab 切换多个问题、Submit 页复查后提交、`Esc` 放弃整个问卷。

输入自由答案时：`Shift+Enter` 换行、`Ctrl+G` 打开 Pi 配置的外部编辑器、`Ctrl+U` 清空草稿。

## 注意事项

- 依赖模型主动调用；模型也可能选择直接执行，若经常被跳过可在提示词/AGENTS.md 中要求"决策前先问"
- 与 powerline 的 fixed-editor 无布局冲突（使用标准对话框，非 widget 渲染）

## 参考

- 本地已装包 README：`%USERPROFILE%\.pi\agent\npm\node_modules\@juicesharp\rpiv-ask-user-question\README.md`
- GitHub：https://github.com/juicesharp/rpiv-mono/tree/main/packages/rpiv-ask-user-question
