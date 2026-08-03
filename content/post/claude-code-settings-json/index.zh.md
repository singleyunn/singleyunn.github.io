---
title: "Claude Code settings.json 配置详解"
description: "解释 Claude Code 的环境变量、模型槽位和权限配置，并区分通用字段与服务商字段。"
date: "2026-08-01"
slug: "claude-code-settings-json"
categories: [AI 工具]
tags: ["Claude Code","配置","权限"]
image: "/img/article-covers/claude-code-settings-json.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 说明：`settings.json` 是 Claude Code 的核心配置文件，控制后端接口、模型选择、权限行为。本文区分**通用字段**（任何后端都能用）和**供应商私有字段**（需按实际替换）。

---

## 一、字段总览

```
settings.json
├── env                          # 环境变量（供应商私有 + 通用开关）
├── permissions                  # 工具调用白/黑名单（通用）
│   ├── allow                    # 白名单：匹配到的调用直接放行
│   └── deny                     # 黑名单：匹配到的调用直接拒绝
└── skipDangerousModePermissionPrompt  # 跳过危险模式确认（通用）
```

---

## 二、通用字段

### 2.1 `skipDangerousModePermissionPrompt`

| 值 | 行为 |
|---|---|
| `true` | 进入 Bypass permissions 模式时不弹确认，所有工具调用直接放行 |
| `false` / 未设 | 每次进入危险模式仍需手动确认 |

等价于命令行启动参数 `--dangerously-skip-permissions`。

⚠️ **风险**：开启后 `rm -rf`、`git push --force`、写任意文件等破坏性操作均无确认弹窗。
仅在**隔离环境 / 容器 / 纯学习项目**中建议开启。

### 2.2 `permissions.allow`（白名单）

匹配到的工具调用直接放行，不再弹确认。

```json
"allow": [
  "Bash(git *)",       // 所有 git 命令
  "Bash(uv *)",        // 所有 uv 命令
  "Bash(ruff *)",      // 所有 ruff 命令
  "Bash(python *)",    // 所有 python 命令
  "Read",              // 读文件
  "Write",             // 写文件
  "Edit",              // 编辑文件
  "NotebookEdit",      // notebook 编辑
  "Glob",              // 文件搜索
  "Grep",              // 内容搜索
  "WebFetch",          // 网页抓取
  "WebSearch",         // 网页搜索
  "mcp__context7__*"   // context7 所有 MCP 工具
]
```

### 2.3 `permissions.deny`（黑名单）

匹配到的调用直接**拒绝**，即使它在白名单里或处于危险模式。

```json
"deny": [
  "Bash(rm -rf *)",
  "Bash(git push --force)",
  "Bash(git reset --hard *)"
]
```

### 2.4 匹配规则速查

| 写法 | 含义 |
|---|---|
| `"Bash(git *)"` | 所有以 `git ` 开头的命令 |
| `"Bash(npm run lint)"` | 精确匹配这一条 |
| `"Read"` | 所有 Read 工具调用 |
| `"mcp__xxx__*"` | 某 MCP 服务器下的全部工具 |

**优先级**：`deny` > `allow` > 默认行为（默认：读放行，写/执行弹确认）

---

## 三、环境变量（env）

分为两类：

### 3.1 通用开关（任何后端都能用）

| 变量 | 说明 |
|---|---|
| `CLAUDE_CODE_MAX_OUTPUT_TOKENS` | 单次回复最大 token 数，默认 8192，可设 131072（128K） |
| `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` | 设为 `1` 禁用非必要遥测/统计流量 |
| `CLAUDE_CODE_ATTRIBUTION_HEADER` | 设为 `"0"` 关闭 cc 归属追踪头（HTTP 审计用） |

### 3.2 供应商私有字段（需按实际替换）

以下示例为 **YOUR_MODEL_ID** 配置，换其他供应商需全部重写：

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

| 变量 | 说明 |
|---|---|
| `ANTHROPIC_BASE_URL` | API 网关地址，每家不同 |
| `ANTHROPIC_AUTH_TOKEN` | 认证令牌，每家不同 |
| `ANTHROPIC_MODEL` | 默认模型（所有槽位） |
| `ANTHROPIC_SMALL_FAST_MODEL` | 快模型槽位 |
| `ANTHROPIC_DEFAULT_SONNET_MODEL` | Sonnet 槽位 |
| `ANTHROPIC_DEFAULT_OPUS_MODEL` | Opus 槽位 |

**槽位全锁定的意义**：4 个槽位全部指定同一模型，确保 Claude Code 在任何上下文中都跑 YOUR_MODEL_ID，不会被服务端偷偷替换模型。不写这些字段时，由服务端决定用哪个模型。

---

## 四、完整配置模板

### 4.1 示例配置

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

### 4.2 通用骨架（按需填入供应商信息）

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

## 五、VS Code 扩展版交互建议

### 5.1 VS Code 用户配置的作用边界

Claude Code for VS Code 常见配置位置：

```text
C:\Users\<用户名>\AppData\Roaming\Code\User\settings.json
```

这个文件是**本机用户级 VS Code 配置**，不属于项目仓库，适合放 Claude Code 扩展的个人环境变量：

```json
"claudeCode.environmentVariables": [
  { "name": "ANTHROPIC_AUTH_TOKEN", "value": "<your-token>" },
  { "name": "ANTHROPIC_BASE_URL", "value": "<gateway-url>" }
]
```

边界原则：

- 可以把中转站 token 放在**个人本机配置**里，例如 VS Code 用户配置或用户级 Claude 配置。
- 不要把真实 token 写进项目共享配置、docs、README、Git 提交、聊天消息。
- 解释用户配置时，不复述真实 token，只说明字段用途。
- 如果用户明确说明配置文件正确，不要反复质疑配置本身，应按用户前提解释字段和交互策略。

### 5.2 `claudeCode.environmentVariables`

关键字段：

| 字段 | 作用 |
|---|---|
| `ANTHROPIC_AUTH_TOKEN` | 中转站或 Anthropic 兼容接口的认证令牌 |
| `ANTHROPIC_BASE_URL` | Anthropic 兼容接口网关地址 |
| `ANTHROPIC_MODEL` | 默认模型；未写时通常由网关或 Claude Code 当前模型选择决定 |
| `ANTHROPIC_DEFAULT_OPUS_MODEL` | Opus 槽位映射 |
| `ANTHROPIC_DEFAULT_SONNET_MODEL` | Sonnet 槽位映射 |
| `ANTHROPIC_DEFAULT_HAIKU_MODEL` | Haiku 槽位映射 |
| `CLAUDE_CODE_SUBAGENT_MODEL` | 子代理模型；是否支持取决于后端/网关 |

只配置 `ANTHROPIC_AUTH_TOKEN` 和 `ANTHROPIC_BASE_URL` 时，模型选择通常由网关默认值、Claude Code 扩展当前选择、用户级 Claude 配置或网关映射共同决定。若要锁定模型，再按供应商文档补充模型槽位字段。

### 5.3 扩展版 Claude Code 的交互设置

常见有用设置：

```json
"claudeCode.preferredLocation": "panel",
"claudeCode.allowDangerouslySkipPermissions": true
```

说明：

- `claudeCode.preferredLocation: "panel"`：让 Claude Code 常驻底部/面板区域，适合边看代码边对话。
- `claudeCode.allowDangerouslySkipPermissions: true`：只是允许扩展使用危险跳过权限模式，不等于当前会话一定处于危险模式。

推荐交互方式：

- 扩展版日常使用 **Auto mode**，效率和安全比较平衡。
- 不确定怎么改时切 **Plan mode**。
- 学习、审计、想逐步看懂改动时用 **Ask before edits**。
- 危险跳过权限模式只适合隔离环境、临时批处理或高度信任场景。

### 5.4 Auto mode 与危险模式的取舍

权限最小化原则仍然有必要，但在 VS Code 扩展版里不一定要手工维护很长的 allowlist。

| 场景 | 建议 |
|---|---|
| 扩展版日常开发 | Auto mode；让 Claude Code 自动判断，必要时确认 |
| 不确定方案/大改前 | Plan mode，先看方案再执行 |
| 学习/审计 | Ask before edits，逐步确认 |
| 大量机械批处理 | 可短时危险模式，最好在隔离目录、worktree 或干净 Git 状态下使用 |
| 删除、推送、改系统配置、处理凭据 | 保留确认或手动执行，不建议危险模式 |

CLI 使用 `claude --dangerously-skip-permissions` 时，所有工具权限都会放开，效率最高，但风险也最高。即使项目里有禁区规则和 hook，也不应把它理解成“无风险默认模式”。使用后应检查：

```powershell
git status
git diff
```

确认没有无关改动、敏感信息、临时文件或误删误改。

---

## 六、常见问题

**Q：`skipDangerousModePermissionPrompt: true` 和 `permissions.allow` 白名单有什么区别？**

- `skipDangerousModePermissionPrompt` 是**全部放行**，进入危险模式后所有工具免确认。
- `allow` 白名单是**部分放行**，只放行匹配项，不在白名单的操作仍需确认。

可组合使用：`allow` 放行高频安全命令，危险模式保留确认兜底。

**Q：`deny` 和 `allow` 冲突时谁赢？**

`denry` 优先。即使某命令在 `allow` 里，只要也命中了 `deny` 规则，就会被拒绝。

**Q：槽位模型必须全部写吗？**

不写也行——未指定的槽位由服务端决定。全部写死是为了锁定模型，避免输出质量波动。

---

*最后更新：2026-07-05*
