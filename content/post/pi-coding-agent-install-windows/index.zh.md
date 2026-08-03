---
title: "Pi Coding Agent：npm 安装与兼容服务配置"
description: "面向 Windows 用户的 Pi 安装、模型配置、验证与安全边界说明。"
date: "2026-08-01"
slug: "pi-coding-agent-install-windows"
categories: [AI 工具]
tags: ["Pi","AI 编程","Windows","配置"]
image: "/img/article-covers/pi-coding-agent-install-windows.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 适用对象：Windows + PowerShell 用户，使用 [Pi Coding Agent](https://github.com/earendil-works/pi) 通过已获授权的 Anthropic Messages API 兼容中转站调用 Claude 模型。
>
> 本机记录日期：2026-07-29；已验证 Pi 版本：`0.82.1`。

## 1. 使用边界

Pi 本身支持官方 Claude、ChatGPT/Codex 等登录方式，也支持 API Key。如果当前环境无法完成官方 Claude 或 GPT 的登录接入，因此本教程记录的是通过已购买、已获授权的第三方中转站接入模型的做法。

这不是 Pi 普遍“不支持 Claude/GPT”的结论，也不应将本教程用于绕过服务地区限制、账户限制或服务条款。中转站的模型可用性、价格、数据处理、日志保留和工具调用兼容性以其服务条款为准；接入前应确认自己有权使用。

## 2. 前置条件

- 已安装 Node.js 22.19 或更高版本，以及 npm。
- 有一个 Anthropic Messages API 兼容中转站的地址和 API Key。
- PowerShell 能访问 npm 镜像及中转站地址。

检查 Node.js 与 npm：

```powershell
node --version
npm --version
```

若 Node.js 版本低于要求，先从 [Node.js 官网](https://nodejs.org/) 安装受支持的 LTS 版本，再重新打开终端。

## 3. 用 npm 安装 Pi

在 PowerShell 中运行：

```powershell
npm install -g @earendil-works/pi-coding-agent
```

安装完成后检查：

```powershell
pi --version
pi --help
```

若提示找不到 `pi`，关闭并重新打开 PowerShell；仍无效时，先查询 npm 的全局前缀。Windows 上全局命令通常安装在该目录，确认该目录已在用户 `PATH` 中：

```powershell
npm prefix -g
```

## 4. 配置中转站

Pi 的用户级配置目录是：

```text
%USERPROFILE%\.pi\agent\
```

本教程会使用以下三个文件：

| 文件 | 用途 | 是否包含密钥 |
|---|---|---|
| `models.json` | 把 Pi 内置 Anthropic 提供方路由到中转站 | 否 |
| `auth.json` | 保存 API Key | 是 |
| `settings.json` | 设定 Pi 默认提供方和模型 | 否 |

### 4.1 设置中转地址

创建或合并 `%USERPROFILE%\.pi\agent\models.json`。下面以本机使用的中转地址为例：

```json
{
  "providers": {
    "anthropic": {
      "baseUrl": "https://your-provider.example"
    }
  }
}
```

这会覆盖 Pi 内置 `anthropic` 提供方的请求地址，但保留 Pi 的内置 Claude 模型目录。不要自行追加 `/v1`：应以中转站针对 Anthropic Messages API 给出的地址为准；上述地址为本机已使用的根地址。

若你的中转站地址不同，仅替换 `baseUrl` 的值。它必须支持 Anthropic Messages API；仅兼容 OpenAI Chat Completions API 的中转站不能使用本节配置。

### 4.2 安全保存 API Key

创建或合并 `%USERPROFILE%\.pi\agent\auth.json`，将占位符替换为你的中转站 API Key：

```json
{
  "anthropic": {
    "type": "api_key",
    "key": "在此填写你的中转站 API Key"
  }
}
```

不要把 Key 写入项目代码、`models.json`、Git 仓库、截图或聊天记录。`auth.json` 仅应保留在用户配置目录，且不应提交版本控制。

Claude Code 使用的 `ANTHROPIC_BASE_URL`、`ANTHROPIC_AUTH_TOKEN`、`CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` 和 `CLAUDE_CODE_ATTRIBUTION_HEADER` 不需要复制到 Pi。Pi 使用 `models.json` 指定中转地址，并从 `auth.json` 读取凭据。

### 4.3 设为默认模型

在 `%USERPROFILE%\.pi\agent\settings.json` 中保留原有字段，并加入或修改以下三项：

```json
{
  "defaultProvider": "anthropic",
  "defaultModel": "claude-sonnet-4-5",
  "defaultThinkingLevel": "medium"
}
```

`claude-sonnet-4-5` 是本机 Pi 0.82.1 可识别的默认模型 ID。中转站未必提供该模型；如调用报“模型不存在”，在 Pi 内输入 `/model` 选择中转站实际提供的模型，或将 `defaultModel` 改成该 ID。

## 5. 验证与使用

先离线检查配置能否被 Pi 读取，不会向中转站发送请求：

```powershell
pi --offline --list-models claude-sonnet-4-5
```

正常情况下会显示 `anthropic` 提供方和 Claude 模型。然后启动 Pi：

```powershell
pi
```

在 Pi 内可输入：

```text
/model
```

选择模型后再发送一条不含敏感内容的短消息确认连通性。此步骤会发送请求，可能产生中转站费用，并将消息内容传给该服务。

## 6. 常见问题

### `401`、`403` 或“未认证”

确认 `auth.json` 中的 Key 无多余空格、已生效且有目标模型权限。密钥泄露、过期或已撤销时，应在中转站后台重新生成，并只更新 `auth.json`。

### `404`、模型不存在或参数不兼容

确认中转站提供 Anthropic Messages API，并核对其支持的模型 ID。部分中转站不支持流式输出、工具调用、图片输入或深度思考；应向中转站确认兼容范围，而不是盲目修改 Pi 配置。

### `pi` 无法启动或提示设置文件锁定

先关闭其他正在运行的 Pi 进程，再重新运行命令。不要在 Pi 正在写入设置时手动删除 `.lock` 文件。

### 想恢复其他默认模型

在 `settings.json` 中把 `defaultProvider` 和 `defaultModel` 改回原值；如需恢复官方 Anthropic 地址，则从 `models.json` 中删除 `providers.anthropic.baseUrl` 覆盖项。是否能使用官方登录仍取决于账户、所在地区和服务商规则。

## 7. 配置检查清单

- [ ] `pi --version` 能正常输出版本。
- [ ] `models.json` 中的地址来自已获授权的 Anthropic 兼容中转站。
- [ ] `auth.json` 中已配置有效 Key，且未提交到 Git。
- [ ] `settings.json` 的默认模型是中转站实际支持的模型。
- [ ] 首次连通测试不包含代码、隐私或其他敏感数据。
