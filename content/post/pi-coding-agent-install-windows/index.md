---
title: "Pi Coding Agent: npm Installation and Relay Service Configuration"
description: "Pi installation for Windows, model configuration, verification, and security boundaries."
date: "2026-08-01"
slug: "pi-coding-agent-install-windows"
categories: [AI 工具]
tags: ["Pi","AI Coding","Windows","Configuration"]
image: "/img/article-covers/pi-coding-agent-install-windows.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

> Audience: Windows + PowerShell users using [Pi Coding Agent](https://github.com/earendil-works/pi) with Claude models via an authorized Anthropic Messages API–compatible relay.
>
> Local record date: 2026-07-29; verified Pi version: `0.82.1`.

## 1. Usage boundaries

Pi itself supports official Claude and ChatGPT/Codex sign-in flows as well as API keys. If official Claude or GPT sign-in isn't available in your current environment, this guide documents connecting through a purchased, authorized third-party relay instead.

This is not a general conclusion that Pi "doesn't support Claude/GPT", and this guide should not be used to circumvent regional restrictions, account restrictions, or terms of service. Model availability, pricing, data handling, log retention, and tool-call compatibility of a relay are governed by its own terms; confirm you are entitled to use it before connecting.

## 2. Prerequisites

- Node.js 22.19 or later, and npm.
- An Anthropic Messages API–compatible relay address and API key.
- PowerShell can reach the npm registry and the relay address.

Check Node.js and npm:

```powershell
node --version
npm --version
```

If the Node.js version is below the requirement, install a supported LTS from the [Node.js website](https://nodejs.org/) first, then reopen the terminal.

## 3. Install Pi via npm

In PowerShell:

```powershell
npm install -g @earendil-works/pi-coding-agent
```

Verify:

```powershell
pi --version
pi --help
```

If `pi` is not found, close and reopen PowerShell; if it still fails, query npm's global prefix. Global commands on Windows usually land in that directory — make sure it's on your user `PATH`:

```powershell
npm prefix -g
```

## 4. Configure the relay

Pi's user-level config directory is:

```text
%USERPROFILE%\.pi\agent\
```

This guide uses three files:

| File | Purpose | Contains secrets |
|---|---|---|
| `models.json` | Routes Pi's built-in Anthropic provider to the relay | No |
| `auth.json` | Stores the API key | Yes |
| `settings.json` | Sets Pi's default provider and model | No |

### 4.1 Set the relay address

Create or merge `%USERPROFILE%\.pi\agent\models.json`. Example with a local relay address:

```json
{
  "providers": {
    "anthropic": {
      "baseUrl": "https://your-provider.example"
    }
  }
}
```

This overrides the request address of Pi's built-in `anthropic` provider while keeping Pi's built-in Claude model catalog. Don't append `/v1` yourself: use the address the relay gives for the Anthropic Messages API; the above is the root address already in use locally.

If your relay address differs, only replace the `baseUrl` value. It must support the Anthropic Messages API; relays that only speak OpenAI Chat Completions cannot use this section's configuration.

### 4.2 Store the API key safely

Create or merge `%USERPROFILE%\.pi\agent\auth.json`, replacing the placeholder with your relay API key:

```json
{
  "anthropic": {
    "type": "api_key",
    "key": "your relay API key here"
  }
}
```

Don't put the key in project code, `models.json`, Git repos, screenshots, or chat logs. `auth.json` should live only in the user config directory and never be committed to version control.

Claude Code's `ANTHROPIC_BASE_URL`, `ANTHROPIC_AUTH_TOKEN`, `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC`, and `CLAUDE_CODE_ATTRIBUTION_HEADER` do not need to be copied over to Pi. Pi uses `models.json` for the relay address and reads credentials from `auth.json`.

### 4.3 Set the default model

In `%USERPROFILE%\.pi\agent\settings.json`, keep existing fields and add or change these three:

```json
{
  "defaultProvider": "anthropic",
  "defaultModel": "claude-sonnet-4-5",
  "defaultThinkingLevel": "medium"
}
```

`claude-sonnet-4-5` is a default model ID recognized by local Pi 0.82.1. The relay may not offer it; if calls error with "model not found", run `/model` inside Pi to pick a model the relay actually provides, or change `defaultModel` to that ID.

## 5. Verification and usage

First, check offline that Pi can read the config without sending any request to the relay:

```powershell
pi --offline --list-models claude-sonnet-4-5
```

Normally this shows the `anthropic` provider and Claude models. Then start Pi:

```powershell
pi
```

Inside Pi you can enter:

```text
/model
```

Pick a model, then send a short message with no sensitive content to confirm connectivity. This step sends a request, may incur relay charges, and transmits the message content to that service.

## 6. Common problems

### `401`, `403`, or "not authenticated"

Check the key in `auth.json` for stray whitespace, that it's active, and that it has permission for the target model. If a key is leaked, expired, or revoked, regenerate it in the relay dashboard and update only `auth.json`.

### `404`, model not found, or incompatible parameters

Confirm the relay provides the Anthropic Messages API and check which model IDs it supports. Some relays don't support streaming, tool calls, image input, or extended thinking; ask the relay about its compatibility scope instead of blindly changing Pi config.

### `pi` won't start or reports a settings file lock

Close other running Pi processes first, then rerun the command. Don't manually delete `.lock` files while Pi is writing settings.

### Restore a different default model

In `settings.json`, change `defaultProvider` and `defaultModel` back; to restore the official Anthropic address, delete the `providers.anthropic.baseUrl` override from `models.json`. Whether official sign-in works still depends on your account, region, and provider rules.

## 7. Configuration checklist

- [ ] `pi --version` outputs a version.
- [ ] The address in `models.json` comes from an authorized Anthropic-compatible relay.
- [ ] `auth.json` has a valid key and is not committed to Git.
- [ ] The default model in `settings.json` is one the relay actually supports.
- [ ] The first connectivity test contains no code, privacy, or other sensitive data.
