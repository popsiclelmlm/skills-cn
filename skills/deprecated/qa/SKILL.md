---
name: qa
description: 交互式 QA session：用户以对话方式报告 bugs 或 issues，agent 创建 GitHub issues。后台探索 codebase 以获取 context 和 domain language。当用户想报告 bugs、做 QA、以对话方式 file issues，或提到 "QA session" 时使用。
---

# QA Session

运行一个交互式 QA session。用户描述遇到的问题。你澄清、探索 codebase 获取 context，并创建 durable、user-focused、使用项目 domain language 的 GitHub issues。

## For each issue the user raises

### 1. Listen and lightly clarify

让用户用自己的话描述 problem。最多问 **2-3 个短 clarifying questions**，聚焦于：

- 他们 expected 什么 vs actually happened 什么
- Steps to reproduce（如果不明显）
- 是否 consistent 或 intermittent

不要 over-interview。如果描述足够清楚可 file，就继续。

### 2. Explore the codebase in the background

与用户交谈时，在后台启动一个 Agent（subagent_type=Explore）来理解相关区域。目标不是找 fix，而是：

- 学习该区域使用的 domain language（检查 UBIQUITOUS_LANGUAGE.md）
- 理解 feature 本应做什么
- 识别 user-facing behavior boundary

这个 context 会帮助你写出更好的 issue；但 issue 本身不应引用具体 files、line numbers 或 internal implementation details。

### 3. Assess scope: single issue or breakdown?

Filing 前，判断这是 **single issue**，还是需要 **broken down** 成多个 issues。

在以下情况拆分：

- Fix 横跨多个 independent areas（例如 “form validation is wrong AND the success message is missing AND the redirect is broken”）
- 存在明显 separable concerns，可由不同人并行处理
- 用户描述的东西有多个不同 failure modes 或 symptoms

在以下情况保持 single issue：

- 这是一个地方的一个错误 behavior
- Symptoms 都由同一个 root behavior 造成

### 4. File the GitHub issue(s)

用 `gh issue create` 创建 issues。不要先让用户 review，直接 file 并分享 URLs。

Issues 必须 **durable**：major refactors 后仍应该讲得通。从用户视角写。

#### For a single issue

使用这个 template：

```
## What happened

[Describe the actual behavior the user experienced, in plain language]

## What I expected

[Describe the expected behavior]

## Steps to reproduce

1. [Concrete, numbered steps a developer can follow]
2. [Use domain terms from the codebase, not internal module names]
3. [Include relevant inputs, flags, or configuration]

## Additional context

[Any extra observations from the user or from codebase exploration that help frame the issue — e.g. "this only happens when using the Docker layer, not the filesystem layer" — use domain language but don't cite files]
```

#### For a breakdown（multiple issues）

按 dependency order 创建 issues（blockers first），这样可以引用真实 issue numbers。

每个 sub-issue 使用这个 template：

```
## Parent issue

#<parent-issue-number> (if you created a tracking issue) or "Reported during QA session"

## What's wrong

[Describe this specific behavior problem — just this slice, not the whole report]

## What I expected

[Expected behavior for this specific slice]

## Steps to reproduce

1. [Steps specific to THIS issue]

## Blocked by

- #<issue-number> (if this issue can't be fixed until another is resolved)

Or "None — can start immediately" if no blockers.

## Additional context

[Any extra observations relevant to this slice]
```

创建 breakdown 时：

- **偏好 many thin issues，而不是 few thick ones**：每个都应可独立修复和验证
- **诚实标记 blocking relationships**：如果 issue B 确实要等 issue A 修好后才能测试，就说明。如果它们独立，两个都标为 "None — can start immediately"
- **按 dependency order 创建 issues**，这样你能在 "Blocked by" 中引用真实 issue numbers
- **最大化 parallelism**：目标是让多个人（或 agents）能同时领取不同 issues

#### Rules for all issue bodies

- **不要写 file paths 或 line numbers**：这些会 stale
- **使用项目的 domain language**（如果存在，检查 UBIQUITOUS_LANGUAGE.md）
- **描述 behaviors，而不是 code**：写 "the sync service fails to apply the patch"，不要写 "applyPatch() throws on line 42"
- **Reproduction steps 是 mandatory**：如果无法确定，就问用户
- **保持 concise**：developer 应能在 30 秒内读完 issue

Filing 后，打印所有 issue URLs（并总结 blocking relationships），然后问："Next issue, or are we done?"

### 5. Continue the session

继续直到用户说完成。每个 issue 独立处理，不要 batch。
