---
title: "VS Code 配置详解"
description: "按功能解释 VS Code 常用设置、编辑器体验和开发环境配置。"
date: "2026-08-01"
slug: "vscode-settings"
categories: [开发环境]
tags: ["VS Code","开发环境","配置"]
image: "/img/article-covers/vscode-settings.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 本项目约定：**所有 VS Code 配置一律写全局用户 settings，不写项目级 `.vscode/settings.json`**。
> 全局文件路径：`%USERPROFILE%\AppData\Roaming\Code\User\settings.json`
> 优先级：项目级 `.vscode/settings.json` > 全局用户 settings。本项目不用项目级，避免覆盖全局造成混乱。

最后更新：2026-07-14

---

## 一、配置分段详解

以下按当前全局 `settings.json` 的内容分组说明每段作用。

### 1. 文件与编辑器基础

```jsonc
"files.associations": { "settings.local.json": "jsonc" },  // 把该文件按带注释的 JSON 高亮
"files.autoSave": "afterDelay",        // 停止输入后自动保存
"files.autoGuessEncoding": true,       // 打开文件时自动猜测编码（防中文乱码）
"editor.fontSize": 15,                 // 编辑器字号（下方 fontsize 是笔误的旧键，实际生效的是 fontSize）
"editor.fontLigatures": false,         // 关闭连字（如 => 不合并成箭头符号）
"editor.mouseWheelZoom": true,         // Ctrl+滚轮缩放字号
"editor.wordWrap": "on",               // 编辑器内长行自动换行
```

> 注意：`"editor.fontsize": 16` 是无效键（正确是 `fontSize`），VS Code 会忽略它。真正生效的是 `"editor.fontSize": 15`。建议清理这条笔误。

### 2. 编辑体验（动画/格式化/建议）

```jsonc
"editor.cursorBlinking": "smooth",              // 光标平滑闪烁
"editor.smoothScrolling": true,                 // 编辑区平滑滚动
"editor.cursorSmoothCaretAnimation": "explicit",// 光标移动平滑动画（仅显式跳转时）
"editor.formatOnPaste": true,                   // 粘贴时自动格式化
"editor.formatOnType": true,                    // 输入时自动格式化
"editor.formatOnSave": true,                    // 保存时自动格式化
"editor.guides.bracketPairs": true,             // 显示括号配对参考线
"editor.acceptSuggestionOnEnter": "smart",      // 回车智能接受补全（避免误触）
"editor.suggestSelection": "recentlyUsed",      // 补全默认选中最近用过的项
"terminal.integrated.smoothScrolling": true,    // 终端平滑滚动
"workbench.list.smoothScrolling": true,         // 列表平滑滚动
```

### 3. 终端配置

```jsonc
"terminal.integrated.profiles.windows": {
    "PowerShell": { "path": "C:\\Program Files\\PowerShell\\7\\pwsh.exe" }, // 指向 pwsh 7
    "Command Prompt": { "path": "C:\\WINDOWS\\System32\\cmd.exe", ... },
    "Git Bash": { "source": "Git Bash", ... }
},
"terminal.integrated.defaultProfile.windows": "PowerShell",  // 默认终端用 PowerShell 7
"terminal.integrated.mouseWheelScrollSensitivity": 3,        // 终端滚轮灵敏度
"terminal.integrated.gpuAcceleration": "off",                // 关闭终端 GPU 加速（防渲染异常）
```

### 4. 界面与主题

```jsonc
"workbench.secondarySideBar.defaultVisibility": "visible", // 默认显示第二侧边栏
"workbench.colorTheme": "Solarized Light",                 // 主题
"workbench.startupEditor": "none",                         // 启动不显示欢迎页
"window.dialogStyle": "custom",                            // 自定义对话框样式
"breadcrumbs.enabled": false,                              // 关闭面包屑导航栏
"explorer.confirmDelete": false,                           // 删除文件不弹确认
"explorer.confirmDragAndDrop": false,                      // 拖放文件不弹确认
```

### 5. Notebook（Jupyter）输出 —— 本次新增重点

```jsonc
"notebook.lineNumbers": "on",              // notebook 单元格显示行号
"notebook.output.wordWrap": true,          // 输出中的长行自动换行（false 则水平滚动）
"notebook.output.textLineLimit": 9999,     // 文本输出展示行数上限
"notebook.output.scrolling": true,         // 输出超限时改为滚动框，而非截断
```

**三条 notebook 输出设置的联动关系（关键）：**

- `notebook.output.wordWrap`：控制**长行是否换行**。`true` = 换行；`false`（默认）= 不换行、需水平滚动。
- `notebook.output.textLineLimit`：控制文本输出**默认展示多少行**。
  - 源码定义：`type: number, default: 30, minimum: 1`。
  - **`-1` 是非法值**（低于 minimum:1 会被回退），不代表"不截断"。想"尽量不截断"只能填大数（此处用 `9999`）。
- `notebook.output.scrolling`：源码描述明确，`textLineLimit` 只有在 `scrolling` 启用时才用于决定输出区域的**滚动高度**。
  - 不开 `scrolling`：输出到达行数上限 → 显示"截断+提示"。
  - 开 `scrolling`：输出到达上限 → 收进**可滚动框**，内容全部保留。
  - 所以 `textLineLimit: 9999` + `scrolling: true` 才共同实现"长输出全保留、装进滚动框、不撑长 notebook"。

> 改动 notebook 这几项需**重载窗口**才生效：命令面板 → `Developer: Reload Window`。

### 6. Python / Jupyter

```jsonc
"python.analysis.completeFunctionParens": true, // 补全函数时自动带括号
"jupyter.jupyterCommandLineArguments": [],       // Jupyter 启动参数（空）
```

### 7. 文件按扩展名的默认打开方式

```jsonc
"workbench.editorAssociations": {
    "*.csv": "gc-excelviewer-csv-editor",    // csv 用表格查看器
    "*.xlsx": "gc-excelviewer-excel-editor", // xlsx 用表格查看器
    "*.parquet": "default",
    "*.duckdb": "default",
    "*.json": "default",
    "*.md": "vscode.markdown.preview.editor" // md 默认开预览
}
```

### 8. Markdown 预览

```jsonc
"markdown-preview-enhanced.previewTheme": "vscode.css",
"markdown-preview-enhanced.revealjsTheme": "vscode.css",
"markdown-preview-enhanced.codeBlockTheme": "vscode.css",
```

### 9. Git

```jsonc
"git.enableSmartCommit": true,        // 无暂存时直接提交全部更改
"git.ignoreMissingGitWarning": true,  // 不提示缺少 git 警告
```

### 10. 更新与扩展（全部关闭自动更新）

```jsonc
"update.mode": "none",                 // 关闭 VS Code 自动更新
"update.showReleaseNotes": false,      // 更新后不弹发行说明
"extensions.autoUpdate": false,        // 扩展不自动更新
"extensions.autoCheckUpdates": false,  // 不自动检查扩展更新
```

### 11. AI / 第三方扩展

```jsonc
"chat.disableAIFeatures": true,        // 关闭内置 Chat AI 功能
"claudeCode.preferredLocation": "panel", // Claude Code 面板停靠位置
"chatgpt.localeOverride": "zh-CN",     // 某 ChatGPT 扩展界面语言
"codebot.*": ...                       // 某代码生成扩展的开关（大多已关）
```

### 12. 其他

```jsonc
"debug.showBreakpointsInOverviewRuler": true, // 概览标尺显示断点
"chat.viewSessions.orientation": "stacked",   // 会话视图堆叠布局
"livePreview.defaultPreviewPath": ...         // Live Preview 默认预览页（原项目级迁移可选）
```

---

## 二、变更记录

> 每次改动全局配置后在此追加一行，方便回溯"什么时候、改了什么、为什么"。

| 日期 | 变更内容 | 涉及键 | 原因 |
|------|----------|--------|------|
| 2026-07-14 | 新增 notebook 输出三项 | `notebook.output.wordWrap: true`、`notebook.output.textLineLimit: 9999`、`notebook.output.scrolling: true` | 长输出自动换行 + 尽量不截断 + 收进滚动框；`-1` 经查源码为非法值故用 9999 |
| 2026-07-14 | 撤回误加到项目级的同名三项 | 项目级 `D:\your-project\.vscode\settings.json` 还原为仅 `livePreview` | 确立"配置一律走全局"约定，避免项目级覆盖全局 |

---

## 三、待清理项（建议）

- `"editor.fontsize": 16` —— 无效键（正确为 `fontSize`），可删除，避免误导。
