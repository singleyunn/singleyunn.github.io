---
title: "Claude Code 权限模式说明"
description: "用通俗方式比较 Ask、Plan、Auto 和 Bypass 等权限模式的风险与适用场景。"
date: "2026-08-01"
slug: "claude-code-permission-modes"
categories: [AI 工具]
tags: ["Claude Code","权限","安全"]
image: "/img/article-covers/claude-code-permission-modes.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

## 五种模式对比

| 模式 | 修改文件 | 执行命令 | 删除文件 | 网络请求 | 适用场景 |
|------|----------|----------|----------|----------|----------|
| **Ask before edits** (默认) | 需确认 | 需确认 | 需确认 | 需确认 | 不信任 AI、逐条审核 |
| **Edit automatically** | 自动放行 | 需确认 | 需确认 | 需确认 | 信任代码修改、不信任命令 |
| **Plan mode** | 禁止 | 禁止 | 禁止 | 只读允许 | 先设计方案再动手 |
| **Auto mode** | 自动判断 | 自动判断 | 自动判断 | 自动判断 | 日常开发，省心推荐 |
| **Bypass permissions** | 全部放行 | 全部放行 | 全部放行 | 全部放行 | 大量自动化操作 |

## 推荐使用策略

```
日常工作中:    Auto mode  （智能判断，简单操作不打扰，高风险才确认）
不确定怎么改:   Plan mode  （先浏览分析，方案确认后切换模式动手）
大量批处理:    Bypass permissions  （效率最高，用完后切回 Auto）
学习/审计:     Ask before edits  （逐条审核每个改动，适合学代码）
```

## Effort 推理深度

控制 Claude 思考和验证的深度：

| 级别 | 速度 | 适用场景 |
|------|------|----------|
| Fast | 最快 | 简单问答、格式化、注释 |
| Default | 中等 | 日常编码 |
| Max | 最慢但最仔细 | 复杂逻辑、数据分析、调试疑难 bug |

## 切换方式

- VSCode 右下角点击当前模式名称 → 弹出列表选择
- 对话框左下角 Permission mode 下拉也可切换
