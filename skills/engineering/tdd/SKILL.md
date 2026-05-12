---
name: tdd
description: 使用 red-green-refactor 循环进行测试驱动开发。当用户想用 TDD 构建 features 或修复 bugs、提到 "red-green-refactor"、想要 integration tests，或要求 test-first development 时使用。
---

# Test-Driven Development

## Philosophy

**Core principle**：Tests 应该通过 public interfaces 验证 behavior，而不是验证 implementation details。Code 可以完全改变；tests 不该因此改变。

**Good tests** 是 integration-style：它们通过 public APIs 触发真实 code paths。它们描述系统做_什么_，而不是_如何_做。一个好 test 读起来像 specification：`user can checkout with valid cart` 会准确告诉你存在什么 capability。这些 tests 能经受 refactors，因为它们不关心内部结构。

**Bad tests** 与 implementation 耦合。它们 mock internal collaborators、测试 private methods，或通过外部手段验证（例如直接查询 database，而不是使用 interface）。警告信号是：你 refactor 时 test 失败了，但 behavior 没变。如果你重命名 internal function 后 tests 失败，那些 tests 测的是 implementation，不是 behavior。

示例见 [tests.md](tests.md)，mocking 指南见 [mocking.md](mocking.md)。

## Anti-Pattern: Horizontal Slices

**不要先写所有 tests，再写所有 implementation。** 这是 “horizontal slicing”，把 RED 当成 “write all tests”，把 GREEN 当成 “write all code”。

这会产生**糟糕 tests**：

- 批量写出的 tests 测的是_想象中的_ behavior，而不是_实际_ behavior
- 你最终测的是事物的_shape_（data structures、function signatures），而不是 user-facing behavior
- Tests 对真实变化不敏感：behavior 坏了也通过，behavior 没问题却失败
- 你会跑在 headlights 之外，在理解 implementation 前就承诺 test structure

**正确方法**：通过 tracer bullets 做 vertical slices。一个 test → 一个 implementation → 重复。每个 test 都回应你从上一个 cycle 学到的东西。因为你刚写了 code，所以你准确知道哪些 behavior 重要，以及如何验证。

```
WRONG (horizontal):
  RED:   test1, test2, test3, test4, test5
  GREEN: impl1, impl2, impl3, impl4, impl5

RIGHT (vertical):
  RED→GREEN: test1→impl1
  RED→GREEN: test2→impl2
  RED→GREEN: test3→impl3
  ...
```

## Workflow

### 1. Planning

探索 codebase 时，使用项目的 domain glossary，让 test names 和 interface vocabulary 匹配项目语言，并尊重你正在触碰区域的 ADRs。

写任何 code 前：

- [ ] 与用户确认需要哪些 interface changes
- [ ] 与用户确认要测试哪些 behaviors（排序优先级）
- [ ] 识别 [deep modules](deep-modules.md) 的机会（small interface, deep implementation）
- [ ] 为 [testability](interface-design.md) 设计 interfaces
- [ ] 列出要测试的 behaviors（不是 implementation steps）
- [ ] 获得用户对 plan 的批准

询问："What should the public interface look like? Which behaviors are most important to test?"

**你不可能测试所有东西。** 和用户确认最重要的 behaviors。把测试努力集中在 critical paths 和 complex logic 上，而不是每个可能 edge case。

### 2. Tracer Bullet

写一个 test，只确认系统的一件事：

```
RED:   Write test for first behavior → test fails
GREEN: Write minimal code to pass → test passes
```

这是你的 tracer bullet：证明 path 能 end-to-end 工作。

### 3. Incremental Loop

对每个剩余 behavior：

```
RED:   Write next test → fails
GREEN: Minimal code to pass → passes
```

Rules:

- 一次一个 test
- 只写足够让当前 test 通过的 code
- 不要预判未来 tests
- Tests 专注于 observable behavior

### 4. Refactor

所有 tests 通过后，寻找 [refactor candidates](refactoring.md)：

- [ ] 提取 duplication
- [ ] Deepen modules（把复杂性移动到 simple interfaces 后面）
- [ ] 在自然处应用 SOLID principles
- [ ] 思考 new code 暴露了 existing code 的哪些问题
- [ ] 每个 refactor step 后运行 tests

**RED 时绝不 refactor。** 先到 GREEN。

## Checklist Per Cycle

```
[ ] Test describes behavior, not implementation
[ ] Test uses public interface only
[ ] Test would survive internal refactor
[ ] Code is minimal for this test
[ ] No speculative features added
```
