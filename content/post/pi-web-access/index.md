---
title: "pi-web-access Extension Guide"
description: "Adds web search, content extraction, GitHub, PDF, and video understanding capabilities to Pi."
date: "2026-08-01"
slug: "pi-web-access"
categories: [AI 工具]
tags: ["Pi","Web Access","Extension"]
image: "/img/article-covers/pi-web-access.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

> Updated: 2026-08-01 | Reference version: 0.17.1 | License: MIT | Requires Pi v0.37.3+ (local 0.83.0 satisfies this)

## Introduction

pi-web-access gives Pi internet capabilities: web search, content extraction, GitHub repo cloning, PDF, and YouTube/local screen-recording understanding.

Core features:

- **Zero-config**: Exa search works by default without an API key; if Pi has a Codex subscription login, OpenAI search reuses its auth
- **Multi-provider fallback chain**: SearXNG → OpenAI → Exa → Brave → Parallel → TinyFish → Search1API → Searchinfinity → Querit → Tavily → SERPdive → Perplexity → Gemini — at least one always works
- **GitHub cloning**: repo URLs clone locally, getting real file contents instead of rendered HTML
- **Video understanding**: full subtitles, frame descriptions, and timestamped frame extraction for YouTube or local videos
- **SSRF protection**: DNS pre-checks targets by default, blocking localhost and private-network IPs

Source: GitHub `nicobailon/pi-web-access` (946★, actively maintained), pi.dev ~161K downloads/month.

## Installation

```bash
pi install npm:pi-web-access
```

Restart Pi for it to take effect. Video frame extraction has optional dependencies (subtitle and frame-description analysis work without them):

```bash
# Installable via winget on Windows; only needed for frame extraction
winget install --id yt-dlp.yt-dlp     # YouTube stream URL resolution for frame extraction
winget install --id Gyan.FFmpeg       # frame extraction, video thumbnails, local video duration
```

> Reference state (2026-08-01): ffmpeg 8.1.2 (winget) and yt-dlp 2026.07.04 (winget) are both globally installed; PATH is updated; new terminals pick it up.

## Uninstallation

```bash
pi remove npm:pi-web-access
```

## Basic usage

The model calls these tools automatically:

```text
web_search({ query: "TypeScript best practices 2025" })
web_search({ query: "latest news", numResults: 10, recencyFilter: "week" })
web_search({ query: "...", provider: "all" })

fetch_content({ url: "https://docs.example.com/guide" })
fetch_content({ url: "https://github.com/owner/repo" })              # auto-clones
fetch_content({ url: "https://youtube.com/watch?v=abc", prompt: "Which libraries are shown?" })
fetch_content({ url: "/path/to/recording.mp4", prompt: "What error appears on screen?" })
```

Common parameters: `numResults` (result count), `recencyFilter` (time filter), `domainFilter` (domain filter), `includeContent` (include full body), `provider` (specify a search provider).

## Configuration

Optional API keys go into `~/.pi/web-search.json`:

```json
{
  "openaiApiKey": "sk-...",
  "braveApiKey": "BSA_...",
  "exaApiKey": "exa-...",
  "perplexityApiKey": "pplx-...",
  "geminiApiKey": "AIza..."
}
```

For relay users: if the OpenAI key comes from a third-party Responses-compatible gateway, set `"openaiResponsesUrl"` to the gateway's full Responses endpoint (default `https://api.openai.com/v1/responses`).

When constrained by a proxy sandbox network (process goes through HTTP_PROXY/HTTPS_PROXY), enable:

```json
{
  "ssrf": { "trustEnvProxy": true }
}
```

Hosts in `NO_PROXY` still get DNS validation; localhost and private IPs are always blocked.

> Common case (2026-08-01): a Clash TUN/fake-IP proxy resolves all domains into the `198.18.0.0/15` range, causing fetch_content to be blocked by the SSRF pre-check for everything ("Blocked internal address"). Written to `~/.pi/web-search.json`:
> `{"ssrf": {"allowRanges": ["198.18.0.0/15"]}}`
> This allowance only affects the fake-IP range; real private addresses (10.x/172.16-31.x/192.168.x/localhost) and NO_PROXY hosts stay blocked, so corporate virtual NIC scenarios are unaffected.

## Notes

- Search and extraction send queries/URLs to the configured third-party services — normal data egress; be careful with sensitive queries
- Default `auto` mode picks providers via the fallback chain; with no keys at all, Exa's free tier is used (rate-limited)
- Video frame extraction needs ffmpeg/yt-dlp; without them, analysis features still work

## References

- Installed package README: `%USERPROFILE%\.pi\agent\npm\node_modules\pi-web-access\README.md`
- GitHub: https://github.com/nicobailon/pi-web-access
