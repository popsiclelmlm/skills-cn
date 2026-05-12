# Language

此 skill 每条 suggestion 共享的 vocabulary。精确使用这些 terms，不要替换成 "component"、"service"、"API" 或 "boundary"。一致语言就是全部重点。

## Terms

**Module**
任何拥有 interface 和 implementation 的东西。故意保持 scale-agnostic：同样适用于 function、class、package 或横跨 tier 的 slice。
_Avoid_：unit、component、service。

**Interface**
Caller 为了正确使用 module 必须知道的一切。包括 type signature，也包括 invariants、ordering constraints、error modes、required configuration 和 performance characteristics。
_Avoid_：API、signature（太窄，只指 type-level surface）。

**Implementation**
Module 内部的东西，即它的 code body。不同于 **Adapter**：一个东西可以是小 adapter 但有大型 implementation（Postgres repo），也可以是大 adapter 但有小 implementation（in-memory fake）。当 seam 是话题时用 "adapter"；否则用 "implementation"。

**Depth**
Interface 上的 leverage：caller（或 test）每学习一单位 interface 能 exercise 的 behaviour 数量。大量 behaviour 位于小 interface 后面时，module 是 **deep**。Interface 几乎和 implementation 一样复杂时，module 是 **shallow**。

**Seam**（来自 Michael Feathers）
你可以在不原地编辑的情况下改变 behaviour 的地方。也就是 module interface 所在的*位置*。选择 seam 放在哪里，本身就是一个 design decision，不同于 seam 后面放什么。
_Avoid_：boundary（会和 DDD 的 bounded context 过载）。

**Adapter**
在 seam 上满足某个 interface 的具体东西。描述的是*角色*（它填充哪个槽位），而不是 substance（内部是什么）。

**Leverage**
Callers 从 depth 获得的东西。每学习一单位 interface 获得更多 capability。一个 implementation 会在 N 个 call sites 和 M 个 tests 中回本。

**Locality**
Maintainers 从 depth 获得的东西。Change、bugs、knowledge 和 verification 集中在一个地方，而不是散落到 callers 中。修一次，到处都修好。

## Principles

- **Depth is a property of the interface, not the implementation.** 一个 deep module 内部可以由 small、mockable、swappable parts 组成，只是它们不是 interface 的一部分。一个 module 可以有 **internal seams**（对 implementation 私有，由它自己的 tests 使用），也可以有 interface 上的 **external seam**。
- **The deletion test.** 想象删除这个 module。如果 complexity 消失，该 module 没有隐藏任何东西（只是 pass-through）。如果 complexity 重新出现在 N 个 callers 中，该 module 就在发挥作用。
- **The interface is the test surface.** Callers 和 tests 穿过同一个 seam。如果你想测试 interface 之外的东西，该 module 的形状大概不对。
- **One adapter means a hypothetical seam. Two adapters means a real one.** 除非某些东西确实跨 seam 变化，否则不要引入 seam。

## Relationships

- 一个 **Module** 正好有一个 **Interface**（它呈现给 callers 和 tests 的 surface）。
- **Depth** 是 **Module** 的属性，相对于它的 **Interface** 衡量。
- **Seam** 是 **Module** 的 **Interface** 所在之处。
- **Adapter** 位于 **Seam** 上，并满足 **Interface**。
- **Depth** 为 callers 产生 **Leverage**，为 maintainers 产生 **Locality**。

## Rejected framings

- **Depth as ratio of implementation-lines to interface-lines**（Ousterhout）：奖励填充 implementation。我们改用 depth-as-leverage。
- 把 **"Interface"** 理解成 TypeScript `interface` keyword 或 class 的 public methods：太窄。这里的 interface 包含 caller 必须知道的每个事实。
- **"Boundary"**：会和 DDD 的 bounded context 过载。说 **seam** 或 **interface**。
