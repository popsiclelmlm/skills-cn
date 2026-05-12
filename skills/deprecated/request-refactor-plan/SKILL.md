---
name: request-refactor-plan
description: 通过用户访谈创建一个包含 tiny commits 的详细 refactor plan，然后把它作为 GitHub issue 提交。当用户想规划 refactor、创建 refactoring RFC，或把 refactor 拆成安全 incremental steps 时使用。
---

当用户想创建 refactor request 时，会调用这个 skill。你应该完成下面步骤。若认为某些步骤不必要，可以跳过。

1. 询问用户，让他们详细描述想解决的问题以及任何潜在 solution ideas。

2. 探索 repo，验证他们的 assertions 并理解 codebase 当前状态。

3. 询问他们是否考虑过其他 options，并向他们展示其他 options。

4. 围绕 implementation 采访用户。要极其详细和彻底。

5. 敲定 implementation 的确切 scope。弄清你计划改变什么，以及计划不改变什么。

6. 查看 codebase，检查该区域是否有 test coverage。如果 test coverage 不足，询问用户的 testing plans。

7. 把 implementation 拆成 tiny commits 的 plan。记住 Martin Fowler 的建议： "make each refactoring step as small as possible, so that you can always see the program working."

8. 用 refactor plan 创建 GitHub issue。Issue description 使用以下 template：

<refactor-plan-template>

## Problem Statement

The problem that the developer is facing, from the developer's perspective.

## Solution

The solution to the problem, from the developer's perspective.

## Commits

A LONG, detailed implementation plan. Write the plan in plain English, breaking down the implementation into the tiniest commits possible. Each commit should leave the codebase in a working state.

## Decision Document

A list of implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)

## Out of Scope

A description of the things that are out of scope for this refactor.

## Further Notes (optional)

Any further notes about the refactor.

</refactor-plan-template>
