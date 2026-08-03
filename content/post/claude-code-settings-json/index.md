---
title: "Claude Code settings.json Configuration Explained"
description: "Claude Code environment variables, model slots, and permission config; distinguishing generic fields from provider-specific ones."
date: "2026-08-01"
slug: "claude-code-settings-json"
categories: [AI 工具]
tags: ["Claude Code","Configuration","Permissions"]
image: "/img/article-covers/claude-code-settings-json.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

> Note: `settings.json` is Claude Code's core config file, controlling the backend API, model selection, and permission behavior. This article distinguishes **generic fields** (usable with any backend) from **provider-specific fields** (must be replaced per your actual setup).

---

## 1. Field overview

```
settings.json
├── env                          # environment variables (provider-specific + generic switches)
├── permissions                  # tool-call allow/deny lists (generic)
│   ├── allow                    # whitelist: matched calls pass through directly
│   └── deny                     # blacklist: matched calls are rejected outright
└── skipDangerousModePermissionPrompt  # skip dangerous-mode confirmation (generic)
```

---

## 2. Generic fields

### 2.1 `skipDangerousModePermissionPrompt`

| Value | Behavior |
|---|---|
| `true` | no confirmation when entering Bypass permissions mode; all tool calls pass through |
| `false` / unset | manual confirmation required each time you enter dangerous mode |

Equivalent to the CLI flag `--dangerously-skip-permissions`.

⚠️ **Risk**: with this on, destructive operations like `rm -rf`, `git push --force`, or writing arbitrary files get no confirmation dialog.
Only enable it in **isolated environments / containers / purely for-learning projects**.

### 2.2 `permissions.allow` (whitelist)

Matched tool calls pass through directly without confirmation.

```json
"allow": [
  "Bash(git *)",       // all git commands
  "Bash(uv *)",        // all uv commands
  "Bash(ruff *)",      // all ruff commands
  "Bash(python *)",    // all python commands
  "Read",              // read files
  "Write",             // write files
  "Edit",              // edit files
  "NotebookEdit",      // notebook editing
  "Glob",              // file search
  "Grep",              // content search
  "WebFetch",          // web fetch
  "WebSearch",         // web search
  "mcp__context7__*"   // all context7 MCP tools
]
```

### 2.3 `permissions.deny` (blacklist)

Matched calls are **rejected outright**, even if whitelisted or in dangerous mode.

```json
"deny": [
  "Bash(rm -rf *)",
  "Bash(git push --force)",
  "Bash(git reset --hard *)"
]
```

### 2.4 Matching rules quick reference

| Pattern | Meaning |
|---|---|
| `"Bash(git *)"` | all commands starting with `git ` |
| `"Bash(npm run lint)"` | exact match for this one |
| `"Read"` | all Read tool calls |
| `"mcp__xxx__*"` | all tools under an MCP server |

**Priority**: `deny` > `allow` > default behavior (default: reads pass, writes/executions prompt)

---

## 3. Environment variables (env)

Two categories:

### 3.1 Generic switches (any backend)

| Variable | Description |
|---|---|
| `CLAUDE_CODE_MAX_OUTPUT_TOKENS` | max tokens per reply; default 8192, settable to 131072 (128K) |
| `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` | set to `1` to disable non-essential telemetry/statistics traffic |
| `CLAUDE_CODE_ATTRIBUTION_HEADER` | set to `"0"` to disable the cc attribution tracking header (for HTTP auditing) |

### 3.2 Provider-specific fields (replace per your setup)

The example below configures **YOUR_MODEL_ID**; switching providers requires rewriting all of them:

```json
"env": {
  "ANTHROPIC_BASE_URL": "https://your-provider.example/anthropic",
  "ANTHROPIC_AUTH_TOKEN": "<your-token>",
  "ANTHROPIC_MODEL": "YOUR_MODEL_ID",
  "ANTHROPIC_SMALL_FAST_MODEL": "YOUR_MODEL_ID",
  "ANTHROPIC_DEFAULT_SONNET_MODEL": "YOUR_MODEL_ID",
  "ANTHROPIC_DEFAULT_OPUS_MODEL": "YOUR_MODEL_ID"
}
```

| Variable | Description |
|---|---|
| `ANTHROPIC_BASE_URL` | API gateway address; different per provider |
| `ANTHROPIC_AUTH_TOKEN` | auth token; different per provider |
| `ANTHROPIC_MODEL` | default model (all slots) |
| `ANTHROPIC_SMALL_FAST_MODEL` | fast model slot |
| `ANTHROPIC_DEFAULT_SONNET_MODEL` | Sonnet slot |
| `ANTHROPIC_DEFAULT_OPUS_MODEL` | Opus slot |

**Why lock all slots**: specifying the same model in all 4 slots ensures Claude Code runs YOUR_MODEL_ID in every context and the server can't silently swap models. Without these fields, the server decides which model to use.

---

## 4. Complete configuration templates

### 4.1 Example config

```json
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "<your-token>",
    "ANTHROPIC_BASE_URL": "https://your-provider.example/anthropic",
    "ANTHROPIC_MODEL": "YOUR_MODEL_ID",
    "ANTHROPIC_SMALL_FAST_MODEL": "YOUR_MODEL_ID",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "YOUR_MODEL_ID",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "YOUR_MODEL_ID",
    "CLAUDE_CODE_MAX_OUTPUT_TOKENS": "131072",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": 1
  },
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Bash(uv *)",
      "Bash(ruff *)",
      "Bash(mypy *)",
      "Bash(python *)",
      "Bash(jupyter *)",
      "Read",
      "Write",
      "Edit",
      "NotebookEdit",
      "Glob",
      "Grep",
      "WebFetch",
      "WebSearch"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force)",
      "Bash(git reset --hard *)"
    ],
    "skipDangerousModePermissionPrompt": true
  }
}
```

### 4.2 Generic skeleton (fill in provider info)

```json
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "<your-token>",
    "ANTHROPIC_BASE_URL": "<gateway-url>",
    "CLAUDE_CODE_MAX_OUTPUT_TOKENS": "131072",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": 1
  },
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Read",
      "Write",
      "Edit",
      "Glob",
      "Grep"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force)"
    ]
  }
}
```

---

## 5. VS Code extension interaction notes

### 5.1 Scope of VS Code user config

Common config location for Claude Code for VS Code:

```text
C:\Users\<username>\AppData\Roaming\Code\User\settings.json
```

This file is **local user-level VS Code config**, not part of the project repo; it's a good place for personal environment variables of the Claude Code extension:

```json
"claudeCode.environmentVariables": [
  { "name": "ANTHROPIC_AUTH_TOKEN", "value": "<your-token>" },
  { "name": "ANTHROPIC_BASE_URL", "value": "<gateway-url>" }
]
```

Boundary principles:

- Relay tokens can live in **personal local config**, e.g. VS Code user config or user-level Claude config.
- Never write real tokens into shared project config, docs, README, Git commits, or chat messages.
- When explaining user config, don't repeat real tokens; only explain the field's purpose.
- If the user explicitly states the config file is correct, don't keep questioning the config itself; explain fields and interaction strategy based on the user's premise.

### 5.2 `claudeCode.environmentVariables`

Key fields:

| Field | Purpose |
|---|---|
| `ANTHROPIC_AUTH_TOKEN` | auth token for the relay or Anthropic-compatible endpoint |
| `ANTHROPIC_BASE_URL` | Anthropic-compatible gateway address |
| `ANTHROPIC_MODEL` | default model; if unset, usually determined by the gateway or Claude Code's current model selection |
| `ANTHROPIC_DEFAULT_OPUS_MODEL` | Opus slot mapping |
| `ANTHROPIC_DEFAULT_SONNET_MODEL` | Sonnet slot mapping |
| `ANTHROPIC_DEFAULT_HAIKU_MODEL` | Haiku slot mapping |
| `CLAUDE_CODE_SUBAGENT_MODEL` | subagent model; support depends on the backend/gateway |

With only `ANTHROPIC_AUTH_TOKEN` and `ANTHROPIC_BASE_URL` set, model selection is usually determined jointly by gateway defaults, the Claude Code extension's current selection, user-level Claude config, or gateway mapping. To lock a model, add the model slot fields per provider docs.

### 5.3 Interaction settings for the extension version

Common useful settings:

```json
"claudeCode.preferredLocation": "panel",
"claudeCode.allowDangerouslySkipPermissions": true
```

Notes:

- `claudeCode.preferredLocation: "panel"`: keeps Claude Code in the bottom/panel area, good for coding while chatting.
- `claudeCode.allowDangerouslySkipPermissions: true`: only *allows* the extension to use dangerous skip-permission mode; it doesn't mean the current session is in dangerous mode.

Recommended interaction patterns:

- Daily use in the extension: **Auto mode**, a good balance of efficiency and safety.
- When unsure how to change something: switch to **Plan mode**.
- Learning, auditing, or wanting to follow each change step by step: use **Ask before edits**.
- Dangerous skip-permission mode fits only isolated environments, temporary batch operations, or high-trust scenarios.

### 5.4 Auto mode vs. dangerous mode trade-offs

Least-privilege still matters, but in the VS Code extension you don't necessarily need to hand-maintain a long allowlist.

| Scenario | Recommendation |
|---|---|
| Daily development in the extension | Auto mode; let Claude Code judge automatically, confirm when necessary |
| Unsure plan / before big changes | Plan mode; review the plan first, then execute |
| Learning/auditing | Ask before edits, confirm step by step |
| Lots of mechanical batch work | Short dangerous-mode stints are OK, best in an isolated dir, worktree, or clean Git state |
| Deleting, pushing, changing system config, handling credentials | keep confirmation or do it manually; dangerous mode not recommended |

With CLI `claude --dangerously-skip-permissions`, all tool permissions open up: maximum efficiency but also maximum risk. Even with guardrail rules and hooks in the project, don't treat it as a "risk-free default mode". After use, check:

```powershell
git status
git diff
```

Confirm there are no unrelated changes, sensitive info, temp files, or accidental deletions/modifications.

---

## 6. Common questions

**Q: What's the difference between `skipDangerousModePermissionPrompt: true` and the `permissions.allow` whitelist?**

- `skipDangerousModePermissionPrompt` is **allow-everything**: once in dangerous mode, all tools skip confirmation.
- The `allow` whitelist is **partial**: only matched items pass; operations not in the whitelist still need confirmation.

They can be combined: `allow` passes high-frequency safe commands, while dangerous mode keeps confirmation as a safety net.

**Q: When `deny` and `allow` conflict, who wins?**

`deny` wins. Even if a command is in `allow`, if it also matches a `deny` rule, it's rejected.

**Q: Must all slot models be written?**

Not required — unspecified slots are decided by the server. Writing all of them locks the model to avoid output quality fluctuation.

---

*Last updated: 2026-07-05*
