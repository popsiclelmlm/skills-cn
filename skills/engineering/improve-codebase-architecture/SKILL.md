---
name: improve-codebase-architecture
description: 根据 CONTEXT.md 中的 domain language 和 docs/adr/ 中的 decisions，在 codebase 中寻找 deepening opportunities。当用户想改进 architecture、寻找 refactoring opportunities、整合 tightly-coupled modules，或让 codebase 更 testable 和 AI-navigable 时使用。
---

# Improve Codebase Architecture

暴露 architectural friction，并提出 **deepening opportunities**：把 shallow modules 变成 deep modules 的 refactors。目标是 testability 和 AI-navigability。

## Glossary

在每条 suggestion 中精确使用这些 terms。语言一致就是重点，不要漂移到 “component”、“service”、“API” 或 “boundary”。完整定义见 [LANGUAGE.md](LANGUAGE.md)。

- **Module** — 任何拥有 interface 和 implementation 的东西（function、class、package、slice）。
- **Interface** — caller 为了正确使用 module 必须知道的一切：types、invariants、error modes、ordering、config。不只是 type signature。
- **Implementation** — 内部 code。
- **Depth** — interface 上的 leverage：小 interface 后面有大量 behaviour。**Deep** = high leverage。**Shallow** = interface 几乎和 implementation 一样复杂。
- **Seam** — interface 所在之处；可以在不原地编辑的情况下改变 behaviour 的地方。（用这个，不用 "boundary"。）
- **Adapter** — 在 seam 上满足 interface 的具体东西。
- **Leverage** — callers 从 depth 得到的东西。
- **Locality** — maintainers 从 depth 得到的东西：change、bugs、knowledge 集中在一个地方。

关键 principles（完整列表见 [LANGUAGE.md](LANGUAGE.md)）：

- **Deletion test**：想象删除这个 module。如果 complexity 消失，它就是 pass-through。如果 complexity 重新出现在 N 个 callers 中，它就是在发挥作用。
- **The interface is the test surface.**
- **One adapter = hypothetical seam. Two adapters = real seam.**

这个 skill 受项目 domain model 启发。Domain language 给好的 seams 命名；ADRs 记录 skill 不应重新争论的 decisions。

## Process

### 1. Explore

先阅读项目的 domain glossary，以及你正在触碰区域的 ADRs。

然后使用 Agent tool，并设置 `subagent_type=Explore` 来遍历 codebase。不要遵循僵硬 heuristics；有机探索，并记录你体验到 friction 的地方：

- 理解一个 concept 是否需要在许多小 modules 之间来回跳？
- 哪里有 **shallow** modules：interface 几乎和 implementation 一样复杂？
- 哪里为了 testability 提取了 pure functions，但真正 bugs 藏在它们被调用的方式中（没有 **locality**）？
- 哪里 tightly-coupled modules 泄漏到了它们的 seams 外？
- Codebase 的哪些部分未测试，或很难通过当前 interface 测试？

对任何你怀疑 shallow 的东西应用 **deletion test**：删除它会集中 complexity，还是只是移动它？“yes, concentrates” 是你想要的信号。

### 2. Present candidates

展示 deepening opportunities 的编号列表。对每个 candidate：

- **Files** — 涉及哪些 files/modules
- **Problem** — 当前 architecture 为什么造成 friction
- **Solution** — 用自然语言描述会改变什么
- **Benefits** — 用 locality 和 leverage 解释，也说明 tests 会如何改善

**使用 CONTEXT.md vocabulary 表达 domain，使用 [LANGUAGE.md](LANGUAGE.md) vocabulary 表达 architecture。** 如果 `CONTEXT.md` 定义了 "Order"，就谈 "the Order intake module"，不要说 "the FooBarHandler"，也不要说 "the Order service"。

**ADR conflicts**：如果 candidate 与现有 ADR 冲突，只有当 friction 真实到值得重新打开 ADR 时才提出。清晰标记（例如 _"contradicts ADR-0007 — but worth reopening because…"_）。不要列出 ADR 禁止的每个理论 refactor。

此时不要提出 interfaces。询问用户："Which of these would you like to explore?"

### 3. Grilling loop

用户选定 candidate 后，进入 grilling conversation。和他们一起走 design tree：constraints、dependencies、deepened module 的形状、seam 后面是什么、哪些 tests 能存活。

当 decisions crystallize 时，side effects 立即发生：

- **用一个不在 `CONTEXT.md` 中的 concept 命名 deepened module？** 把该 term 添加到 `CONTEXT.md`，纪律与 `/grill-with-docs` 相同（见 [CONTEXT-FORMAT.md](../grill-with-docs/CONTEXT-FORMAT.md)）。如果文件不存在，惰性创建。
- **在对话中打磨 fuzzy term？** 立刻更新 `CONTEXT.md`。
- **用户用 load-bearing reason 拒绝 candidate？** 提出 ADR，表述为：_"Want me to record this as an ADR so future architecture reviews don't re-suggest it?"_ 只有当这个 reason 未来 explorer 确实需要知道、以避免再次提出同一建议时才提出；跳过短暂理由（"not worth it right now"）和显而易见的理由。见 [ADR-FORMAT.md](../grill-with-docs/ADR-FORMAT.md)。
- **想为 deepened module 探索 alternative interfaces？** 见 [INTERFACE-DESIGN.md](INTERFACE-DESIGN.md)。
