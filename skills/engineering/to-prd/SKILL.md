---
name: to-prd
description: 把当前 conversation context 转成 PRD，并发布到项目 issue tracker。当用户想从当前上下文创建 PRD 时使用。
---

这个 skill 接收当前 conversation context 和 codebase understanding，并产出 PRD。不要 interview 用户，只综合你已经知道的内容。

Issue tracker 和 triage label vocabulary 应该已经提供给你；如果没有，运行 `/setup-matt-pocock-skills`。

## Process

1. 如果还没有探索 repo，先探索以理解 codebase 当前状态。整个 PRD 中使用项目的 domain glossary vocabulary，并尊重你正在触碰区域的任何 ADRs。

2. 勾勒完成 implementation 需要构建或修改的 major modules。主动寻找可以提取 deep modules 的机会，让它们能被隔离测试。

Deep module（相对于 shallow module）是指：它用一个简单、testable 且很少变化的 interface 封装大量 functionality。

和用户确认这些 modules 是否符合他们的预期。和用户确认他们希望为哪些 modules 编写 tests。

3. 使用下面的 template 编写 PRD，然后发布到项目 issue tracker。应用 `ready-for-agent` triage label，不需要额外 triage。

<prd-template>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## User Stories

A LONG, numbered list of user stories. Each user story should be in the format of:

1. As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

This list of user stories should be extremely extensive and cover all aspects of the feature.

## Implementation Decisions

A list of implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)

## Out of Scope

A description of the things that are out of scope for this PRD.

## Further Notes

Any further notes about the feature.

</prd-template>
