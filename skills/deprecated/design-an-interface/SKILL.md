---
name: design-an-interface
description: 使用 parallel sub-agents 为一个 module 生成多个激进不同的 interface designs。当用户想设计 API、探索 interface options、比较 module shapes，或提到 "design it twice" 时使用。
---

# Design an Interface

基于 "A Philosophy of Software Design" 中的 "Design It Twice"：你的第一个想法不太可能是最好的。生成多个激进不同的 designs，然后比较。

## Workflow

### 1. Gather Requirements

设计前，理解：

- [ ] 这个 module 解决什么 problem？
- [ ] Callers 是谁？（other modules、external users、tests）
- [ ] Key operations 是什么？
- [ ] 有哪些 constraints？（performance、compatibility、existing patterns）
- [ ] 哪些应隐藏在内部，哪些应暴露？

询问："What does this module need to do? Who will use it?"

### 2. Generate Designs（Parallel Sub-Agents）

使用 Task tool 同时 spawn 3+ sub-agents。每个都必须产出一个**激进不同**的方法。

```
Prompt template for each sub-agent:

Design an interface for: [module description]

Requirements: [gathered requirements]

Constraints for this design: [assign a different constraint to each agent]
- Agent 1: "Minimize method count - aim for 1-3 methods max"
- Agent 2: "Maximize flexibility - support many use cases"
- Agent 3: "Optimize for the most common case"
- Agent 4: "Take inspiration from [specific paradigm/library]"

Output format:
1. Interface signature (types/methods)
2. Usage example (how caller uses it)
3. What this design hides internally
4. Trade-offs of this approach
```

### 3. Present Designs

展示每个 design，并包含：

1. **Interface signature** — types、methods、params
2. **Usage examples** — callers 实际如何使用它
3. **What it hides** — kept internal 的 complexity

按顺序展示 designs，让用户能在比较前吸收每种方法。

### 4. Compare Designs

展示所有 designs 后，按以下方面比较：

- **Interface simplicity**：更少 methods、更简单 params
- **General-purpose vs specialized**：flexibility vs focus
- **Implementation efficiency**：shape 是否允许 efficient internals？
- **Depth**：小 interface 隐藏显著 complexity（好）vs 大 interface 搭配 thin implementation（坏）
- **Ease of correct use** vs **ease of misuse**

用 prose 讨论 trade-offs，不用 tables。突出 designs 差异最大的地方。

### 5. Synthesize

最好的 design 往往结合多个 options 的 insights。询问：

- "Which design best fits your primary use case?"
- "Any elements from other designs worth incorporating?"

## Evaluation Criteria

来自 "A Philosophy of Software Design"：

**Interface simplicity**：更少 methods、更简单 params = 更容易学习并正确使用。

**General-purpose**：能无需 changes 就处理 future use cases。但要警惕 over-generalization。

**Implementation efficiency**：Interface shape 是否允许 efficient implementation？还是强迫 awkward internals？

**Depth**：小 interface 隐藏显著 complexity = deep module（好）。大 interface 搭配 thin implementation = shallow module（避免）。

## Anti-Patterns

- 不要让 sub-agents 产出相似 designs；强制 radical difference
- 不要跳过 comparison；价值在 contrast 中
- 不要 implement；这里只讨论 interface shape
- 不要基于 implementation effort 评估
