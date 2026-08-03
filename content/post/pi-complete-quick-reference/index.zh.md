---
title: "Pi Coding Agent 从零到进阶：快捷键与启动界面速查"
description: "整理 Pi 的启动方式、会话命令、快捷键、模型切换和日常使用方法。"
date: "2026-08-01"
slug: "pi-complete-quick-reference"
categories: [AI 工具]
tags: ["Pi","命令速查","Windows"]
image: "/img/article-covers/pi-complete-quick-reference.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 适用于 pi v0.83.x。覆盖日常使用闭环和本机已验证的 Windows 操作；不重复罗列每个光标移动按键，完整键位以 `/hotkeys` 为准。
> 记不住快捷键时输入 **`/hotkeys`** 查看 pi 内置帮助；**Ctrl+O** 只负责展开或折叠工具输出。

---

## 第一课：三分钟跑通（最小闭环）

**目标：** 打开 pi、提问、退出。

```powershell
# 一步进入工作目录并启动 pi（PowerShell 7 支持 &&）
cd D:\your-workspace && pi
```

启动后看到 `>` 就是输入框：

| 操作 | 按键 | 说明 |
|---|---|---|
| 提问 | 直接打字，按 **Enter** | 例如输入「今天的日期」回车 |
| 中断 | **Esc** | AI 回答到一半想停止 |
| 退出 | **Ctrl+D** | 输入框为空时按，退出 pi |

> 💡 第一次用：就做这三件事，跑通一遍就行。其他全在后面。

---

## 第二课：输入文字、文件与图片

**目标：** 打字、换行、改字、引用文件和发送图片。

| 按键或写法 | 作用 |
|---|---|
| **Enter** | 发送输入 |
| **Shift+Enter** | 插入换行；部分 Windows 终端可能无法区分它与普通回车 |
| **Ctrl+Enter** | Windows Terminal 中插入换行 |
| **Ctrl+J** | 插入换行的通用别名 |
| **Ctrl+C** | 清空输入框；选中文字时为复制 |
| **Ctrl+-** | 撤销刚才的输入修改 |
| **@** | 模糊搜索并引用当前项目文件 |
| `@完整路径` | 引用项目外或已知路径的文件，例如 `@D:\图片\截图.png` |

### 发送剪贴板图片

Windows 上优先使用：

```text
Alt+V
```

本机已验证：按 **Alt+V** 后，Pi 会把剪贴板图片临时保存为类似以下路径，并作为图片附件加入输入：

```text
%USERPROFILE%\AppData\Local\Temp\pi-clipboard-<随机ID>.png
```

这个路径是 Pi 自动生成的临时文件，不需要手动输入，也不应作为长期项目素材引用。需要长期保存的截图应另存到项目或图片目录。

其他发送方式：

- **Ctrl+V**：部分终端可以直接粘贴图片；如果只得到文字或路径，改用 **Alt+V**。
- **拖拽图片**：把图片拖入终端输入区。
- **引用文件**：输入 `@D:\图片\截图.png`，或输入 `@` 后搜索项目内图片。

图片加入后继续输入问题，再按 **Enter** 一并发送。能否真正识图取决于当前模型；用 `/model` 查看模型，或在终端运行 `pi --list-models` 检查 `images` 列。没有图片能力的模型只能收到路径或拒绝附件。

> Windows 终端行为可能不同：如果 Shift+Enter 直接发送，请使用 Ctrl+Enter 或 Ctrl+J；如果 Ctrl+V 不能粘贴图片，请使用 Alt+V。

---

## 第三课：管理你的会话（最重要！）

**目标：** 每次对话都能起名、能找回，不丢上下文。

### 什么是会话？

每次你启动 pi 聊天，就是一个**会话**。会话会自动保存到 `%USERPROFILE%\.pi\agent\sessions\`（按工作目录分文件夹），**下次可以原样继续**。

### 给会话起名字（两种方式）

```powershell
# 方式一：启动时起名
pi -n "速查文档更新"          # -n 就是 --name

# 方式二：已经进入了，中途改名
/name 速查文档更新
```

### 找回之前的会话（按名字找）

```powershell
pi -r
```

`pi -r` 会打开会话选择列表，里面支持：

| 按键 | 作用 |
|---|---|
| **打字搜索** | 直接输入关键字过滤，如「速查」「安装」 |
| **Ctrl+N** | 只看**已命名**的会话（配合上面命名用） |
| **Ctrl+R** | 给选中的会话重新起名 |
| **Ctrl+D** | 删除会话（会先确认，慎用） |
| **Ctrl+P** | 切换显示文件路径 |
| **Ctrl+S** | 切换排序方式 |

选中后回车，就进入那个会话，AI 能读到里面的完整历史。

### 其他会话操作

| 命令 | 作用 |
|---|---|
| `pi -c` | 继续**最近一次**的会话（最常用！） |
| `/new` | 当前会话里开一个全新的会话 |
| `/resume` | 同 `pi -r`，在会话内打开选择列表 |
| `/session` | 查看当前会话的文件、ID、消息数、用量 |
| `pi --session <ID>` | 用会话 ID 精确进入（不常用，知道即可） |

### 自动压缩与 `/compact`

Pi 默认同样支持自动上下文压缩。状态栏末尾出现 **`AC`**，表示 Auto Compaction 已开启；一般不需要按固定百分比手动执行。

自动触发条件是：

```text
当前上下文 tokens > 模型上下文上限 - reserveTokens
```

默认 `reserveTokens` 为 16,384，因此触发百分比取决于模型上限：

| 模型上下文上限 | 默认自动触发点（约） |
|---:|---:|
| 128K | 87.2% |
| 200K | 91.8% |
| 1M | 98.4% |

压缩后，Pi 默认保留最近约 20K tokens 的原始消息，并把更早内容整理成结构化摘要；目标、关键决策、进度和文件操作会被保留，但细枝末节可能丢失。

**实用建议：**

- 普通连续任务：直接依赖 `AC`，不用盯着百分比。
- 即将进入新阶段或准备开始一次很长的操作：可在 **70%–80%** 且任务处于自然边界时手动压缩。
- 没有自然边界但想提前留出余量：到 **85%–90%** 再手动执行即可。
- 不建议在 30%–60% 频繁压缩；过早摘要既增加一次模型调用，也可能损失细节。

需要手动压缩时可附带摘要要求：

```text
/compact 保留目标、约束、已完成事项、关键决策、文件改动、验证结果、未解决问题和下一步
```

直接输入 `/compact` 时，Pi 的默认结构化摘要本来就会整理 Goal、Constraints、Progress、Key Decisions、Next Steps、Critical Context，以及已读/已修改文件；上面的长版指令是再次强调重点，并非每次都必须输入。

自动压缩通常在一轮回答结束后检查阈值；如果请求先因上下文溢出失败，Pi 也可以自动压缩后重试。以 1M 上下文模型为例，69% 尚未达到默认约 98.4% 的自动触发点，因此从 69% 降到约 25% 是本次手动 `/compact` 的结果。压缩后不会降到 0%，因为结构化摘要、最近消息、系统指令、项目规则和工具定义仍然占用上下文。

全局配置位于 `%USERPROFILE%\.pi\agent\settings.json`，默认值为：

```json
{
  "compaction": {
    "enabled": true,
    "reserveTokens": 16384,
    "keepRecentTokens": 20000
  }
}
```

不建议为了某个 1M 模型把全局 `reserveTokens` 大幅调高：切换到 128K 模型后会过早触发。日常保持默认自动压缩、在阶段边界偶尔手动执行最稳妥。

### 推荐习惯

1. 开始新任务时：`pi -n "任务名"`（如 `pi -n "小红书文案"`）
2. 下次继续：`pi -c` 直接续上最近一个
3. 想找更早的：`pi -r` → Ctrl+N → 打字 → 回车
4. 忘了命名的老会话：`pi -r` 里按 **Ctrl+R** 补名字

---

## 第四课：控制回复、模型与消息队列

**目标：** 高效控制 AI 的回复、输出、模型和排队消息。

| 按键 | 作用 |
|---|---|
| **Esc** | 中断 AI 当前回答；有排队消息时也会恢复到输入框 |
| **Ctrl+O** | 展开/折叠工具输出（AI 执行命令的过程详情） |
| **Ctrl+X** | 复制 AI 刚回复的最后一条消息 |
| **Ctrl+L** | 打开模型选择器 |
| **Ctrl+P** | 在主界面切到下一个模型；在会话选择列表中切换路径显示 |
| **Ctrl+Shift+P** | 切到上一个模型 |
| **Shift+Tab** | 循环切换 thinking 强度 |
| **Ctrl+T** | 折叠/展开 thinking 内容，不改变强度 |
| **↑ / ↓** | 在列表中选择；**Enter** 确认，**Esc** 取消 |

### AI 工作时继续发消息

| 操作 | 行为 |
|---|---|
| **Enter** | 排入 steering 消息；当前回答执行完已有工具调用后交给模型 |
| **Alt+Enter** | 排入 follow-up；当前任务全部结束后再发送 |
| **Alt+↑** | 把排队消息取回输入框修改 |
| **Esc** | 中断当前任务并把排队消息恢复到输入框 |

> Windows Terminal 默认可能把 Alt+Enter 用作全屏快捷键。如果 Pi 收不到该按键，需要在 Windows Terminal 设置中取消或改绑全屏快捷键。

### Ctrl+G：用外部编辑器写长内容

**原理：** pi 把输入框里的草稿写进临时 `prompt.md`，打开外部编辑器，**等你关掉后再读回**（临时文件自动删除）。

**流程：** 打字 → **Ctrl+G** → 记事本自动打开 → 写完保存并关闭 → 内容回到输入框 → **Enter** 发送。

**想换 Typora？** 编辑 `%USERPROFILE%\.pi\agent\settings.json`，加一行（改完执行 `/reload` 生效）：

```json
"externalEditor": "D:\\存储\\typora\\Typora\\typora.exe"
```

> ⚠️ Typora 是单实例软件：若桌面上已有打开的 Typora 窗口，Ctrl+G 会失败（pi 以为你编辑完了）。**用前先关掉其他 Typora 窗口。**

---

## 第五课：看懂启动界面（状态栏）

**目标：** 启动时看到的英文不再一头雾水。

### 启动横幅

```
pi v0.83.0                          ← pi 版本号
[Context] AGENTS.md                 ← 已加载的项目规则文件
[Skills] ai-publish-analysis,...    ← 已加载的技能（用于特定任务）
[Prompts] /gather-context-and-clarify,... ← 预置提示词
[Extensions] @juicesharp/rpiv-...   ← 已加载的扩展插件
What's New                          ← 本次版本更新日志（可忽略）
```

顶部还有一行快捷键提示：`escape interrupt`（Esc 中断）、`ctrl+c/ctrl+d clear/exit`（清空/退出）、`/ commands`（命令菜单）、`! bash`（执行命令）、`ctrl+o more`（展开/折叠工具输出）。完整快捷键帮助请使用 `/hotkeys`。

### 底部状态栏（从左到右）

```
DeepSeek V4 Flash | think:max | dir xzy_ | ⎇ main *6 +1 ?6 | ◫ 0/1.0M (0.0%) AC
```

| 内容 | 含义 |
|---|---|
| `DeepSeek V4 Flash` | 当前使用的模型（用 `/model` 可切换） |
| `think:max` | 深度思考等级：off / minimal / low / medium / high / xhigh / max |
| `dir xzy_` | 当前工作目录 |
| `⎇ main *6 +1 ?6` | Git 状态：分支 `main`；`*` 修改数、`+` 已暂存数、`?` 未跟踪数 |
| `◫ 0/1.0M (0.0%) AC` | 上下文用量：已用 0 / 共 1M tokens；AC = 自动压缩已开启 |

### 切换模型与思考强度

| 操作 | 方法 | 生效范围 |
|---|---|---|
| 打开模型选择器 | `/model` 或 **Ctrl+L** | 当前会话 |
| 循环切换 thinking 等级 | **Shift+Tab** | 当前会话，状态栏 `think:*` 会立即变化 |
| 折叠/展开 thinking 内容 | **Ctrl+T** | 只改变显示，不改变思考强度 |
| 设置默认 thinking 等级 | `/settings` → `defaultThinkingLevel` | 以后启动的新会话 |

thinking 等级依次为 `off`、`minimal`、`low`、`medium`、`high`、`xhigh`、`max`。模型只支持其中一部分时，pi 会跳过不支持的等级或将其限制到可用等级。

也可以在启动时指定。例如，使用 ChatGPT/Codex OAuth 接入的 `gpt-5.6-sol`：

```powershell
# 分开指定模型和思考强度
pi --model openai-codex/gpt-5.6-sol --thinking medium

# 模型参数的简写形式
pi --model openai-codex/gpt-5.6-sol:high
```

日常任务可从 `medium` 开始，复杂排障或架构分析再临时切到 `high`。`xhigh`、`max` 通常更慢，也可能更快消耗服务商用量。

---

## 第六课：斜杠命令完整速查（/ 开头）

在输入框输入 `/` 会打开命令补全菜单；扩展、Skills 和提示模板还可能注册额外命令。

| 命令 | 作用 |
|---|---|
| `/login`、`/logout` | 登录或退出 OAuth、API Key 提供方 |
| `/model` | 切换当前模型 |
| `/scoped-models` | 设置 Ctrl+P 可以循环的模型范围和顺序 |
| `/settings` | 修改 thinking、主题、消息投递和传输等常用设置 |
| `/new` | 新建空白会话 |
| `/name <名字>` | 给当前会话命名 |
| `/resume` | 打开历史会话选择器 |
| `/session` | 查看会话文件、ID、消息数、token 和估算 cost |
| `/tree` | 查看会话树，并从任意历史节点继续 |
| `/fork` | 从之前的用户消息创建新分支会话 |
| `/clone` | 复制当前活动分支为新会话 |
| `/compact [要求]` | 压缩较早上下文，可附带摘要要求 |
| `/trust` | 保存当前项目或父目录的信任决定；通常需重启生效 |
| `/copy` | 复制最后一条助手消息 |
| `/export [文件]` | 导出会话为 HTML 或 JSONL |
| `/import <文件>` | 导入并继续 JSONL 会话 |
| `/share` | 上传为私有 GitHub Gist 并生成分享链接；属于外部发布，执行前确认内容 |
| `/reload` | 重载快捷键、扩展、Skills、提示模板、主题和上下文文件 |
| `/hotkeys` | 查看完整快捷键 |
| `/changelog` | 查看 Pi 版本更新记录 |
| `/llama` | 管理 llama.cpp 路由模型；未使用本地模型时可忽略 |
| `/quit` | 退出 Pi |

两个 shell 前缀：

| 前缀 | 作用 |
|---|---|
| `!命令` | 执行系统命令，并把输出发送给模型，如 `!git status` |
| `!!命令` | 执行系统命令但**不把输出发给模型**，适合自己临时查看 |

命令会在当前项目目录执行。不要通过命令打印密钥、Cookie、完整 `auth.json` 或其他敏感信息。

---

## 第七课：启动命令写法大全

### 工作目录与会话

```powershell
# ★ 每天最常用：进入工作目录并启动 Pi
cd D:\your-workspace && pi

# 继续最近一次会话
pi -c

# 浏览并选择历史会话
pi -r

# 启动时命名
pi -n "我的任务名"

# 精确进入指定会话
pi --session <会话ID>

# 临时会话，不写入会话历史
pi --no-session
```

### 模型与 thinking

```powershell
# 查看可用模型，可附搜索词
pi --list-models
pi --list-models openai

# 指定提供方和模型
pi --provider deepseek --model deepseek-v4-flash

# 提供方/模型 + thinking 简写
pi --model openai-codex/gpt-5.6-sol:high

# 分开指定 thinking
pi --model openai-codex/gpt-5.6-sol --thinking medium

# 限制 Ctrl+P 循环范围
pi --models "openai-codex/*,deepseek/*"
```

### 一次性任务与文件输入

```powershell
# 提问一次，打印结果后退出
pi -p "总结当前项目"

# 启动时带入文件或图片
pi @README.md "总结这个文档"
pi "@D:\图片\截图.png" "分析这张截图"

# 读取标准输入
Get-Content README.md | pi -p "总结这份内容"
```

### 工具与启动联网行为

```powershell
# 禁用全部工具，仅聊天
pi --no-tools

# 只允许只读工具
pi --tools read,grep,find,ls

# 禁止某个工具
pi --exclude-tools bash

# 禁用启动阶段的更新检查、包更新和遥测请求
pi --offline
```

`--offline` 只关闭 Pi 的**启动网络操作**，不会让需要联网的远程模型变成本地模型。

---

## 第八课：模型认证和配置文件

### 登录方式

在 Pi 输入：

```text
/login
```

常见两类认证：

| 类型 | 示例 | 特点 |
|---|---|---|
| OAuth | `openai-codex`（ChatGPT Plus/Pro） | 浏览器授权，令牌可自动刷新 |
| API Key | DeepSeek、自定义中转站 | 静态 Key，撤销、过期或余额不足后需处理 |

使用 ChatGPT 订阅时应选择 `openai-codex/...` 模型；普通 `openai/...` 通常属于独立计费的 OpenAI API，不是同一套额度。

### 三个主要配置文件

```text
%USERPROFILE%\.pi\agent\auth.json       OAuth 或 API Key，严禁分享或提交 Git
%USERPROFILE%\.pi\agent\models.json     自定义提供方、baseUrl、模型或覆盖
%USERPROFILE%\.pi\agent\settings.json   默认模型、thinking、主题和全局设置
```

OpenAI/Codex、DeepSeek 等内置提供方不一定出现在 `models.json`；它们的模型目录由 Pi 内置或缓存，凭据保存在 `auth.json`。

`/session` 和底部状态栏显示的是当前会话 token 与估算 cost，不是 OpenAI 或 DeepSeek 账户余额。账户剩余额度以服务商官方 Usage 页面为准。

---

## 第九课：代理、TUN 与 OAuth 回调

### 推荐：只开启 TUN

TUN 虚拟网卡在网络层接管浏览器和 Pi；通常不需要再打开 Windows 系统代理。OAuth 浏览器授权后会回到：

```text
http://127.0.0.1:随机端口/callback
```

同时开启 TUN 和系统代理时，系统代理可能干扰这个本地回调。本机验证成功的组合是：**TUN 开启、Windows 系统代理关闭**。

### 不开 TUN、只用本地代理

仅打开 Windows 系统代理时，浏览器通常会走代理，但 Pi 不保证自动读取 WinINET 设置。可以从当前 PowerShell 临时设置：

```powershell
$env:HTTP_PROXY  = 'http://127.0.0.1:7897'
$env:HTTPS_PROXY = 'http://127.0.0.1:7897'
$env:NO_PROXY    = 'localhost,127.0.0.1,::1'
pi
```

端口必须改为客户端实际端口。这些变量只对当前 PowerShell 和它启动的 Pi 生效，关闭窗口后失效。

更完整的动态连通性、TUN、系统代理及 OAuth 排障记录见 fcclient VPN 配置优化报告。

---

## 第十课：常见问题与每日工作流

### 常见问题

| 现象 | 优先检查 |
|---|---|
| 图片无法粘贴 | 改按 Alt+V；确认终端和当前模型支持图片 |
| OAuth 页面打不开 | 节点是否能访问 `auth.openai.com`、`chatgpt.com`，出口地区是否受支持 |
| OAuth 授权成功但 Pi 没收到 | 关闭系统代理仅保留 TUN，或让 localhost 绕过代理 |
| 登录成功但模型调用超时 | TUN 是否真正能访问模型 API；仅开系统代理时设置 `HTTP_PROXY`、`HTTPS_PROXY` |
| `/model` 找不到模型 | 检查 `/login`、凭据和 `pi --list-models <关键词>` |
| thinking 看似没变化 | Shift+Tab 才改变等级；Ctrl+T 只控制显示；模型可能不支持全部等级 |
| 上下文快满 | `/compact` 压缩，或 `/new` 开新会话 |
| 找不到旧任务 | `pi -r` 搜索，平时用 `/name` 或 `pi -n` 命名 |
| 修改配置未生效 | `/reload`；涉及项目 trust 或启动级设置时重启 Pi |

### 推荐每日流程

1. `cd` 到项目目录，用 `pi -n "任务名"` 启动。
2. 输入 `@` 引用文件；截图用 Alt+V。
3. `/model` 或 Ctrl+L 选择模型，Shift+Tab 选择 thinking。
4. 复杂任务先让 Pi 读取规则和相关文件，再授权修改。
5. 用 `/session` 查看上下文；过长时 `/compact`。
6. 完成后确认测试、Git 差异和提交；下次使用 `pi -c` 或 `pi -r` 继续。

---

## 学习路线建议

| 阶段 | 学什么 | 目标 |
|---|---|---|
| 第 1 天 | 第一、二课 | 能提问、换行、引用文件、发送图片和退出 |
| 第 1 周 | 第三、四课 | 会话不丢，能控制模型、thinking 和排队消息 |
| 熟悉后 | 第五、六、七课 | 看懂界面，掌握命令和启动参数 |
| 进阶 | 第八、九、十课 | 理解认证、代理、排障和稳定工作流 |

---

## 版本与核验依据

本手册基于本机 Pi v0.83.x 自带文档 `usage.md`、`keybindings.md`、`providers.md`、`settings.md` 和 `environment-variables.md` 整理，并结合本机实际验证：

- `Alt+V` 可将剪贴板图片作为临时 PNG 附加到输入；
- ChatGPT Plus/Pro 可通过 `/login` 写入 `openai-codex` OAuth 凭据；
- `Shift+Tab` 改变 thinking，`Ctrl+T` 只控制 thinking 内容显示；
- TUN 开启、Windows 系统代理关闭时 OAuth loopback 回调成功。

升级 Pi 后先运行 `pi --version`、`/changelog` 和 `/hotkeys`，若新版本行为变化，应同步更新本手册。
