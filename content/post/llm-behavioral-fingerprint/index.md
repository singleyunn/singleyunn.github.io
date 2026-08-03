---
title: "One Token to Identify a Wrapper Model: Random Numbers as AI Behavioral Fingerprints"
description: "LLMs are not truly random but consistently favor certain numbers, colors, and choices. Researchers combine these preferences into behavioral fingerprints to detect whether an API relay secretly swaps models."
date: "2026-08-03"
slug: "llm-behavioral-fingerprint"
categories: [AI 工具]
tags: ["LLM","Behavioral Fingerprint","API Relay","Security","Paper"]
image: "/img/article-covers/llm-behavioral-fingerprint.svg"
toc: true
comments: false
license: false
---

A paper titled *One Token Is Enough* ran a deceptively simple experiment: repeatedly ask 165 AI models "Give me a random number from 1 to 100."

Each model was asked 30 times, then the answer distribution was tallied.

The result: the numbers produced by large models are far less random than you might think.

## Every model has its own "lucky number"

GPT-4o more often answers 42, 37, and 57; Claude Sonnet 5 clearly favors 47; Llama 3.3 often picks 53.

More extreme: Qwen3-Max (as listed in the original post) answered 42 in all 30 trials.

The researchers did not stop at random numbers. They also tested random colors, animals, cities, coin flips, and other tasks — 10 categories in total, sending roughly 326,000 requests to OpenRouter.

The conclusion points to the same phenomenon: different models exhibit distinct, relatively stable probability preferences.

## A flaw can become an ID card

"That models are not truly random" is not a new finding. The more interesting part is that the paper combines multiple preferences into a *Behavioral Fingerprint*.

Its use is straightforward: first collect a reference fingerprint from a trusted official API, then collect the same kind of answers from the relay API being verified, and compare whether the two probability distributions are close enough.

This helps determine whether a paid model endpoint actually runs the model it claims to serve.

This is not purely theoretical. The original post mentions that CISPA researchers audited 17 relay APIs, using capability tests, statistical checks, and LLMmap fingerprinting to find multiple endpoints where the model did not match.

Traditional methods usually require dedicated test questions, full answers, model databases, and classifiers — hard for ordinary users. The value of the random-number method is compressing complex probing into an unremarkable everyday question:

```text
Give me a random number from 1 to 100.
```

Such questions can also be rephrased in other languages or wordings, making it harder for a relay service to detect the probe and temporarily switch models.

## What one token can achieve

The protocol designed in the paper resembles biometric authentication: compute the Jensen–Shannon divergence between two probability distributions; if the distance is below a threshold, authentication passes.

According to the original post, using all 40 probe units gives an error rate of about 7.3%; using only 8 units (~120 requests) gives about 10.6%.

One notable case appeared in the study: Palmyra X5 on OpenRouter showed a behavioral fingerprint very close to the open-source model Qwen3-235B.

The authors stay cautious, describing it as a statistical observation rather than an accusation of intent. Data and code are public:

```text
https://zenodo.org/records/21278557
```

## Why AI cannot be truly random

The original post extends the question to the coin-flip bias seen in AI-generated videos.

A truly uniform random distribution from 1 to 100 should have an entropy of 6.64 bits; the median across the 165 models cited was only about 1.0 bit — meaning answers are highly concentrated and predictable.

Humans show similar biases: we avoid repeated or adjacent digits, favor odd numbers, primes, and numbers that look "non-round." 7, 37, 42, 47, and 53 carry cultural meaning or "look more random," which ironically makes them more frequent.

A large model learns from massive amounts of human text; every weight encodes the patterns and preferences of its corpus. When asked to "say any number," it can only generate the answer that most resembles a random number in its training data.

Model names can be rewritten, system prompts can be modified, and another wrapper can be placed around the API. But probability habits formed through long-term training can still leak out through a single token.

## Reference

- Original author: 数字生命卡兹克 (@Khazix0918)
- Source:

```text
https://x.com/Khazix0918/status/2079433865517793752
```
