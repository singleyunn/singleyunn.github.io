---
title: "AI 编程成本真能省一半吗？模型之外，还有个 Harness"
description: "Databricks 用真实内部任务测试了多种 AI 编程模型和 Agent Harness。最值得关注的不是谁赢了，而是上下文管理、单任务成本和成功率应该放在一起看。"
date: "2026-08-03"
slug: "ai-coding-cost-harness"
categories: [AI 工具]
tags: ["AI 编程","成本","Agent Harness","上下文","Databricks"]
image: "/img/article-covers/ai-coding-cost-harness.svg"
toc: true
comments: false
license: false
---

最近看到 Databricks 做了一轮 AI 编程工具内部测试，我觉得挺有意思。

以前选工具，我也会先看模型：哪个更聪明，哪个 Token 单价更便宜。

但这组数据里，真正拉开成本差距的，还有模型外面的那层"壳"。

这层"壳"通常叫 Agent Harness。简单理解，模型是大脑，Harness 是工作台，负责把文件、工具、历史记录和测试结果交给模型，再执行模型给出的操作。

同一个模型放进不同的工作台，花的钱和做事效果都可能不一样。

## 这份测试怎么做的

Databricks 没有直接采用 SWE-Bench 等公开题库，而是从自己的真实 PR 中构建任务。

这些任务来自一个数百万行、多语言的内部代码库，覆盖 Python、Go、TypeScript、Scala、Rust、Java 等技术栈。测试用例被提前留出，等 Agent 表示任务完成后再加入验证。

为了防止 Agent 从 Git 历史里找到原答案，测试环境还切断了仓库历史。最终结果由测试是否通过来判断，没有使用另一个大模型充当裁判。

我比较认可的一点是，它没有用另一个大模型来判断结果，而是直接看隐藏测试能不能通过。

当然，这仍然是一份内部基准。任务数量、原始数据、完整配置和统计区间没有全部公开，外界也没法完整复现。

所以我会把它当成一份有参考价值的企业样本，而不是所有项目都能直接照搬的排行榜。

## 我更关心的是：钱省在哪儿

这次对比里，我最关注的是 Opus 4.8 在 Claude Code 和 Pi 两种 Harness 下的表现。

不同推理档位的数据差别挺大：

- high 推理档位下，Pi 约便宜 2.08 倍，成功率为 85%，Claude Code 为 87%。
- xhigh 档位下，Pi 约便宜 1.46 倍，成功率为 90%，Claude Code 为 88%。
- max 档位下，Pi 约便宜 1.20 倍，成功率为 82%，Claude Code 为 89%。

把这三组放在一起看，我的理解是：Harness 确实可能成为一个很明显的成本杠杆，但能省多少、成功率会不会变化，都跟具体推理档位和任务有关。

有些配置能把 Token 成本压下去很多，有些配置的差距就没那么大。选工具时，还是要把成本和成功率放在一起看。

## 大头在上下文

这组数据里，对我最有启发的是上下文。

以 Opus 4.8 为例，Claude Code 每个任务重复输入的上下文中位数约为 74.2 万 Token，Pi 约为 23.6 万 Token，前者大约是后者的 3.2 倍。

上下文越长，每一轮调用的输入费用通常越高。无关历史、过多工具说明和没有及时压缩的旧信息，都可能成为成本。

不过，上下文少也不等于一定更好。有些内容承担着权限控制、安全提醒、工具兼容和复杂任务恢复等作用。

更轻的 Harness 通常更灵活，使用者也可能需要自己补齐沙箱、权限和工作流能力。这部分省下来的 Token，最后会不会变成额外的配置和维护成本，也要一起看。

## 别只盯 Token 单价

同一份测试还比较了 Sonnet 5 和 Opus 4.8。

Sonnet 5 的 Token 单价大约便宜 1.7 倍，但完成一个任务平均花费 2.09 美元；Opus 4.8 平均为 1.94 美元。

原因是 Sonnet 5 在完成任务时消耗了约 1.9 倍的 Token，最终成功率也低了 6 个百分点，分别是 81% 和 87%。

这个结果让我重新看了一下"便宜模型"这件事。

Token 单价只是成本的一部分。一个模型如果需要反复读文件、尝试和返工，即使单价更低，最后也不一定更省。

真正值得看的指标，是"完成一个有效任务花多少钱"，而不是"每百万 Token 多少钱"。

## 开源模型值得放进候选名单

GLM 5.2 搭配 Pi 后，在这份基准中进入了最高能力档位，质量与测试中的 Opus 4.8 配置在统计上接近。

它的平均任务成本是 1.28 美元，作为对比，文中选择的 Opus 4.8 配置是 1.94 美元。

这个结果至少说明，开源模型已经值得放进真实项目的候选名单，而不是只拿来做简单任务。

如果换成不同代码库、语言、提示质量、部署方式和推理服务，结果都可能变化。我不会因为一份内部测试就直接换掉现有模型，但会愿意拿自己的任务再跑一轮对比。

## 如果是我，我会这么选

如果现在让我选 AI 编程工具，我会重点看四件事：

1. 不只看 Token 单价，同时记录单任务总成本和成功率。
2. 看 Harness 每轮到底传了多少上下文，是否存在大量重复输入。
3. 把订阅费用、等待时间、人工返工、安全能力和维护成本一起算进去。
4. 拿自己过去的真实任务做一轮小测试，再决定模型和工具组合。

我更愿意把 Pi 看成一个轻量、可定制的选项，而不是标准答案。Claude Code、Codex 和其他 Harness 也各有自己的工具、安全能力和工作流取舍。

工具选择没必要站队。先把自己的任务成本、成功率和上下文消耗测清楚，再决定该换模型，还是该调整 Harness 和工作流。

> 内容由"碳基漫游 AI"整理发布，用于个人学习与记录。

## 参考

### 1. Databricks 内部基准

标题：Benchmarking Coding Agents on Databricks' Multi-Million Line Codebase

作者：Vinay Gaba、Ankit Mathur、Rishabh Singh、Patrick Wendell、Matei Zaharia

发布方：Databricks Blog

发布时间：2026 年 7 月 8 日

地址：

```text
https://www.databricks.com/blog/benchmarking-coding-agents-databricks-multi-million-line-codebase
```

### 2. Pi Coding Agent

作者：Mario Zechner

类型：官方介绍、文档与开源代码

官网：

```text
https://pi.dev/
```

代码仓库：

```text
https://github.com/earendil-works/pi
```

### 3. GLM-5.2

作者/机构：Z.ai

类型：官方模型介绍与开源代码

模型介绍：

```text
https://z.ai/blog/glm-5.2
```

代码仓库：

```text
https://github.com/zai-org/GLM-5
```

### 4. 本次整理的原始公众号材料

标题：不用换模型，换个壳就能省一半成本

作者：原文未标注

地址：

```text
https://mp.weixin.qq.com/s/Kq-AOXXwtZ311xSyXPKMIw
```
