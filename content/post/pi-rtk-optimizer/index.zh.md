---
title: "pi-rtk-optimizer 扩展使用说明"
description: "介绍如何用 rtk 优化命令输出，减少终端工具结果对上下文的占用。"
date: "2026-08-01"
slug: "pi-rtk-optimizer"
categories: [AI 工具]
tags: ["Pi","扩展","上下文优化"]
image: "/img/article-covers/pi-rtk-optimizer.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 更新日期：2026-08-01 ｜ 参考版本：0.9.0 ｜ 许可：MIT

## 工具介绍

pi-rtk-optimizer 从两条路径减少 context 消耗（= 省钱，尤其按量计费的中转站用户）：

1. **命令重写**：自动把 `bash` 工具命令改写为 `rtk` 等价命令（决策交给已安装的 `rtk rewrite`，Pi 只做 Windows 安全修正）；`rtk` 二进制缺失时原样执行并停止重复探测
2. **输出压缩管线**：多阶段压缩工具输出（`bash`/`read`/`grep`）：
   - ANSI 剥离 → 测试输出聚合（pass/fail 计数）→ 构建输出只留错误/警告 → git 输出压缩 → lint 聚合 → grep/rg 按文件分组 → 源码注释/空白过滤（none/minimal/aggressive）→ 智能截断（80 行内 read 保持精确）→ **锚点安全**（hashline 锚定行保留完整编辑锚点）→ 最终硬截断

来源：GitHub `MasuRii/pi-rtk-optimizer`（215★，MIT），pi.dev 下载量约 11K/月。

## 安装

```bash
pi install npm:pi-rtk-optimizer
```

## 卸载

```bash
pi remove npm:pi-rtk-optimizer
```

## 基本使用

安装后自动生效（需环境中有 `rtk` 命令才启用重写，否则原样执行）。命令：

| 命令 | 作用 |
|---|---|
| `/rtk` | 打开 Tabbed 设置面板（←/→ 切页、Enter/Space 改值、Esc 关闭，实时生效） |
| `/rtk show` | 显示当前配置与运行状态 |
| `/rtk path` | 显示配置文件路径 |
| `/rtk verify` | 检查 `rtk` 二进制是否可用 |
| `/rtk stats` | 查看各类工具输出的压缩节省统计 |

## 配置要点

- 命令重写支持「自动改写」与「仅建议」两种模式，在 `/rtk` 面板切换
- 源码过滤等级：`none`（不过滤）/ `minimal` / `aggressive`（激进去注释与空白，保留 userscript 元数据）
- 锚点安全是默认保护：检测到 hashline 锚定的 `read` 输出时，过滤/截断会保留完整编辑锚点，避免破坏后续编辑

## 注意事项

- `rtk` 需另行安装（Rust Token Killer，`rtk-ai/rtk`，Apache 2.0），未安装时扩展退化为纯输出压缩，功能不报错
  - Windows 全局安装：`winget install rtk-ai.rtk`（注意有同名冒牌货 Rust Type Kit，必须用 `rtk-ai.rtk` 这个 ID）
  - 验证正品：`rtk gain` 能输出 token 节省统计即为正品
  - 装好后需 `/reload` 让扩展重新探测二进制
  - 参考状态（2026-08-01）：已装 rtk 0.44.0，`rtk --version` 与 `rtk gain` 均正常
- 压缩只影响进入 context 的文本，不影响工具实际执行
- 与 pi-tool-display 同作者、可同装；极端压缩可能丢失少量细节，按需调 aggressive 等级

## 参考

- 本地已装包 README：`%USERPROFILE%\.pi\agent\npm\node_modules\pi-rtk-optimizer\README.md`
- GitHub：https://github.com/MasuRii/pi-rtk-optimizer
