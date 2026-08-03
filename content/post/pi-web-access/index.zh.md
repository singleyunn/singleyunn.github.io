---
title: "pi-web-access 扩展使用说明"
description: "为 Pi 增加网页搜索、内容提取、GitHub、PDF 和视频理解能力。"
date: "2026-08-01"
slug: "pi-web-access"
categories: [AI 工具]
tags: ["Pi","网页访问","扩展"]
image: "/img/article-covers/pi-web-access.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 更新日期：2026-08-01 ｜ 参考版本：0.17.1 ｜ 许可：MIT ｜ 要求 Pi v0.37.3+（本机 0.83.0 满足）

## 工具介绍

pi-web-access 给 Pi 提供联网能力：网页搜索、内容提取、GitHub 仓库克隆、PDF、YouTube/本地录屏理解。

核心特性：

- **零配置可用**：默认 Exa 免 API key 搜索；若 Pi 有 Codex 订阅登录，OpenAI 搜索可复用其认证
- **多 provider 回退链**：SearXNG → OpenAI → Exa → Brave → Parallel → TinyFish → Search1API → Searchinfinity → Querit → Tavily → SERPdive → Perplexity → Gemini，总有一条能用
- **GitHub 克隆**：仓库 URL 直接本地克隆，拿到真实文件内容而非渲染 HTML
- **视频理解**：YouTube 或本地视频的完整字幕、画面描述、指定时间戳抽帧
- **SSRF 防护**：默认对目标做 DNS 预检，屏蔽 localhost 与内网 IP

来源：GitHub `nicobailon/pi-web-access`（946★，活跃维护），pi.dev 下载量约 161K/月。

## 安装

```bash
pi install npm:pi-web-access
```

装完重启 Pi 生效。视频抽帧可选依赖（没有也能做字幕与画面描述分析）：

```bash
# Windows 可用 winget 安装；仅抽帧功能需要
winget install --id yt-dlp.yt-dlp     # YouTube 流地址解析用于抽帧
winget install --id Gyan.FFmpeg       # 帧提取、视频缩略图、本地视频时长
```

> 参考状态（2026-08-01）：ffmpeg 8.1.2（winget）与 yt-dlp 2026.07.04（winget）均已全局安装，PATH 已更新，新开终端生效。

## 卸载

```bash
pi remove npm:pi-web-access
```

## 基本使用

模型会自动调用以下工具：

```text
web_search({ query: "TypeScript best practices 2025" })
web_search({ query: "latest news", numResults: 10, recencyFilter: "week" })
web_search({ query: "...", provider: "all" })

fetch_content({ url: "https://docs.example.com/guide" })
fetch_content({ url: "https://github.com/owner/repo" })              # 自动克隆
fetch_content({ url: "https://youtube.com/watch?v=abc", prompt: "展示了哪些库？" })
fetch_content({ url: "/path/to/recording.mp4", prompt: "屏幕上出现什么错误？" })
```

常用参数：`numResults`（结果数）、`recencyFilter`（时效过滤）、`domainFilter`（域名过滤）、`includeContent`（含正文）、`provider`（指定搜索商）。

## 配置

可选 API key 写入 `~/.pi/web-search.json`：

```json
{
  "openaiApiKey": "sk-...",
  "braveApiKey": "BSA_...",
  "exaApiKey": "exa-...",
  "perplexityApiKey": "pplx-...",
  "geminiApiKey": "AIza..."
}
```

中转站用户注意：若 OpenAI key 来自第三方 Responses 兼容网关，可设置 `"openaiResponsesUrl"` 指向网关的完整 Responses 端点（默认 `https://api.openai.com/v1/responses`）。

受代理沙箱网络限制时（进程走 HTTP_PROXY/HTTPS_PROXY），可开启：

```json
{
  "ssrf": { "trustEnvProxy": true }
}
```

`NO_PROXY` 内的主机仍会做 DNS 校验，localhost 与私网 IP 始终被拦截。

> 常见案例（2026-08-01）：Clash TUN/fake-IP 代理会把所有域名解析到 `198.18.0.0/15` 段，导致 fetch_content 被 SSRF 预检全部拦截（报 "Blocked internal address"）。已写入 `~/.pi/web-search.json`：
> `{"ssrf": {"allowRanges": ["198.18.0.0/15"]}}`
> 该放行只影响 fake-IP 段，真实内网地址（10.x/172.16-31.x/192.168.x/localhost）与 NO_PROXY 主机仍被拦截，不影响公司虚拟网卡场景。

## 注意事项

- 搜索与提取会把查询/URL 发送到所配置的第三方服务，属正常数据外发；敏感查询慎用
- 默认 `auto` 模式按回退链自动选 provider；无任何 key 时走 Exa 免费额度（有限速）
- 视频抽帧需要 ffmpeg/yt-dlp，缺失时分析功能仍可用

## 参考

- 本地已装包 README：`%USERPROFILE%\.pi\agent\npm\node_modules\pi-web-access\README.md`
- GitHub：https://github.com/nicobailon/pi-web-access
