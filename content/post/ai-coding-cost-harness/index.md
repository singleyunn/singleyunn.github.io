---
title: "Can AI Coding Really Cost Half as Much? Look Past the Model at the Harness"
description: "Databricks benchmarked coding models and Agent harnesses on real internal tasks. The lesson is not who won, but that context management, per-task cost, and success rate should be evaluated together."
date: "2026-08-03"
slug: "ai-coding-cost-harness"
categories: [AI 工具]
tags: ["AI Coding","Cost","Agent Harness","Context","Databricks"]
image: "/img/article-covers/ai-coding-cost-harness.svg"
toc: true
comments: false
license: false
---

I recently came across an internal AI coding tool benchmark by Databricks, and found it quite interesting.

Previously, when choosing tools, I also looked at the model first: which one is smarter, which has a cheaper per-token price.

But in this data, the real cost gap is also driven by the "shell" around the model.

This shell is usually called the Agent Harness. Simply put, the model is the brain; the harness is the workbench. It feeds files, tools, history, and test results to the model, then executes the operations the model proposes.

The same model in different workbenches can differ in both money spent and results achieved.

## How the benchmark was built

Databricks did not directly use public benchmarks like SWE-Bench. Instead, they constructed tasks from their own real pull requests.

These tasks come from a multi-million-line, multi-language internal codebase covering Python, Go, TypeScript, Scala, Rust, Java, and more. Test cases were held out and added back only after the Agent claimed a task was done.

To prevent Agents from finding the original answers in Git history, the test environment also severed the repository history. Results were judged purely by whether tests passed — no separate large model served as judge.

One thing I particularly appreciate: they did not use another LLM to judge results; they simply checked whether hidden tests passed.

Of course, this is still an internal benchmark. Task counts, raw data, full configuration, and confidence intervals are not fully public, and outsiders cannot fully reproduce it.

So I treat it as a valuable enterprise sample, not a leaderboard to copy verbatim for every project.

## What I care about: where the money is saved

In this comparison, I focused on Opus 4.8 running under two harnesses: Claude Code and Pi.

The differences across reasoning levels are significant:

- At the `high` reasoning level, Pi was ~2.08x cheaper with an 85% success rate; Claude Code hit 87%.
- At `xhigh`, Pi was ~1.46x cheaper with 90% success; Claude Code hit 88%.
- At `max`, Pi was ~1.20x cheaper with 82% success; Claude Code hit 89%.

Looking at these three groups together, my take: the harness can indeed be a significant cost lever, but how much you save and whether success rate moves depend on the specific reasoning level and task.

Some configurations cut token costs dramatically; others show little gap. When choosing tools, weigh cost and success rate together.

## The big cost driver is context

The most instructive part of this data, for me, is context.

Take Opus 4.8: the median repeated input context per task was about 742K tokens for Claude Code versus about 236K for Pi — roughly a 3.2x difference.

Longer context generally means higher input cost per round. Irrelevant history, excessive tool descriptions, and stale information that was not compacted can all become cost.

However, less context is not automatically better. Some content serves permission control, safety reminders, tool compatibility, and complex-task recovery.

Lighter harnesses are usually more flexible, but users may need to fill in sandboxing, permissions, and workflow capabilities themselves. Whether the tokens saved turn into extra configuration and maintenance cost is also part of the equation.

## Do not stare only at token price

The same benchmark compared Sonnet 5 and Opus 4.8.

Sonnet 5's token price is roughly 1.7x cheaper, but it averaged $2.09 per completed task; Opus 4.8 averaged $1.94.

The reason: Sonnet 5 consumed about 1.9x more tokens per task and ended with a success rate 6 points lower — 81% versus 87%.

This made me rethink "cheap models."

Token price is only part of the cost. A model that needs to re-read files, retry, and rework may end up costing more even at a lower unit price.

The metric that really matters is "how much does one effective task cost," not "how much per million tokens."

## Open-source models deserve a place on the shortlist

GLM 5.2 paired with Pi reached the top capability tier in this benchmark, statistically close to the tested Opus 4.8 configuration.

Its average task cost was $1.28, versus $1.94 for the Opus 4.8 configuration chosen in the article.

At minimum, this shows open-source models are worth including in candidate lists for real projects — not just for simple tasks.

Results could change with different codebases, languages, prompt quality, deployment setups, and inference services. I would not switch models based on one internal test, but I would happily run my own tasks through a comparison round.

## How I would choose

If I had to pick AI coding tools today, I would focus on four things:

1. Track not just token price, but total cost and success rate per task.
2. Look at how much context each harness sends per round and whether there is heavy repeated input.
3. Include subscription fees, wait time, human rework, safety capabilities, and maintenance cost.
4. Run a small test with my own past tasks before deciding on a model and tool combination.

I prefer to treat Pi as a lightweight, customizable option rather than the standard answer. Claude Code, Codex, and other harnesses each have their own tools, safety capabilities, and workflow trade-offs.

There is no need to pick sides. Measure your own task cost, success rate, and context consumption first — then decide whether to change models or adjust the harness and workflow.

## Reference

### 1. Databricks internal benchmark

Title: Benchmarking Coding Agents on Databricks' Multi-Million Line Codebase

Authors: Vinay Gaba, Ankit Mathur, Rishabh Singh, Patrick Wendell, Matei Zaharia

Publisher: Databricks Blog

Published: July 8, 2026

URL:

```text
https://www.databricks.com/blog/benchmarking-coding-agents-databricks-multi-million-line-codebase
```

### 2. Pi Coding Agent

Author: Mario Zechner

Type: official introduction, docs, and open-source code

Website:

```text
https://pi.dev/
```

Repository:

```text
https://github.com/earendil-works/pi
```

### 3. GLM-5.2

Author/Organization: Z.ai

Type: official model introduction and open-source code

Model page:

```text
https://z.ai/blog/glm-5.2
```

Repository:

```text
https://github.com/zai-org/GLM-5
```

### 4. Original WeChat article used in this summary

Title: 不用换模型，换个壳就能省一半成本

Author: not stated

URL:

```text
https://mp.weixin.qq.com/s/Kq-AOXXwtZ311xSyXPKMIw
```
