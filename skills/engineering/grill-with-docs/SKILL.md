---
name: grill-with-docs
description: 一场 grilling session，会用既有 domain model 挑战你的计划、打磨 terminology，并在 decisions crystallise 时内联更新 documentation（CONTEXT.md、ADRs）。当用户想用项目语言和已记录决策 stress-test 一个计划时使用。
---

<what-to-do>

围绕这个计划的每个方面 relentlessly interview 我，直到我们达成共享理解。沿着 design tree 的每个分支走下去，逐一解决 decisions 之间的 dependencies。对每个问题，提供你的推荐答案。

一次只问一个问题，并在继续前等待每个问题的反馈。

如果某个问题能通过探索 codebase 回答，就探索 codebase，而不是提问。

</what-to-do>

<supporting-info>

## Domain awareness

在 codebase exploration 期间，也寻找现有 documentation：

### File structure

大多数 repos 有单一 context：

```
/
├── CONTEXT.md
├── docs/
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

如果 root 下存在 `CONTEXT-MAP.md`，该 repo 有多个 contexts。map 会指向每个 context 所在位置：

```
/
├── CONTEXT-MAP.md
├── docs/
│   └── adr/                          ← system-wide decisions
├── src/
│   ├── ordering/
│   │   ├── CONTEXT.md
│   │   └── docs/adr/                 ← context-specific decisions
│   └── billing/
│       ├── CONTEXT.md
│       └── docs/adr/
```

惰性创建 files：只有当你有东西要写时才创建。如果没有 `CONTEXT.md`，在第一个 term 被解决时创建。如果没有 `docs/adr/`，在第一个 ADR 被需要时创建。

## During the session

### Challenge against the glossary

当用户使用了与 `CONTEXT.md` 中现有语言冲突的 term，立刻指出来。"Your glossary defines 'cancellation' as X, but you seem to mean Y — which is it?"

### Sharpen fuzzy language

当用户使用模糊或 overloaded terms，提出一个 precise canonical term。"You're saying 'account' — do you mean the Customer or the User? Those are different things."

### Discuss concrete scenarios

讨论 domain relationships 时，用具体 scenarios stress-test 它们。发明能探测 edge cases 的 scenarios，迫使用户精确说明 concepts 之间的 boundaries。

### Cross-reference with code

当用户陈述某事如何工作时，检查 code 是否同意。如果发现矛盾，展示出来："Your code cancels entire Orders, but you just said partial cancellation is possible — which is right?"

### Update CONTEXT.md inline

当一个 term 被解决时，立刻更新 `CONTEXT.md`。不要批处理这些内容；在它们发生时捕获。使用 [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md) 中的格式。

不要把 `CONTEXT.md` 和 implementation details 耦合。只包含对 domain experts 有意义的 terms。

### Offer ADRs sparingly

只有以下三点都为真时，才提出创建 ADR：

1. **Hard to reverse** — 之后改变主意的成本有意义
2. **Surprising without context** — 未来读者会疑惑 “why did they do it this way?”
3. **The result of a real trade-off** — 曾有真正 alternatives，并且你基于具体原因选择了其中一个

如果三者缺一，跳过 ADR。使用 [ADR-FORMAT.md](./ADR-FORMAT.md) 中的格式。

</supporting-info>
