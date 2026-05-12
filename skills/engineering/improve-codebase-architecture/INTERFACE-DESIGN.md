# Interface Design

当用户想为选定的 deepening candidate 探索 alternative interfaces 时，使用这个 parallel sub-agent pattern。基于 "Design It Twice"（Ousterhout）：你的第一个想法不太可能是最好的。

使用 [LANGUAGE.md](LANGUAGE.md) 中的 vocabulary：**module**、**interface**、**seam**、**adapter**、**leverage**。

## Process

### 1. Frame the problem space

Spawn sub-agents 前，为选定 candidate 写一段面向用户的问题空间解释：

- 新 interface 需要满足的 constraints
- 它会依赖的 dependencies，以及它们属于哪个 category（见 [DEEPENING.md](DEEPENING.md)）
- 一个粗略 illustrative code sketch 来锚定 constraints；这不是 proposal，只是让 constraints 具体化

把它展示给用户，然后立刻进入 Step 2。用户阅读和思考时，sub-agents 会并行工作。

### 2. Spawn sub-agents

使用 Agent tool 并行 spawn 3+ sub-agents。每个都必须为 deepened module 产出一个**激进不同**的 interface。

给每个 sub-agent 一个独立 technical brief（file paths、coupling details、来自 [DEEPENING.md](DEEPENING.md) 的 dependency category、seam 后面是什么）。该 brief 独立于 Step 1 的 user-facing problem-space explanation。给每个 agent 不同的 design constraint：

- Agent 1："Minimize the interface — aim for 1–3 entry points max. Maximise leverage per entry point."
- Agent 2："Maximise flexibility — support many use cases and extension."
- Agent 3："Optimise for the most common caller — make the default case trivial."
- Agent 4（如果适用）："Design around ports & adapters for cross-seam dependencies."

在 brief 中同时包含 [LANGUAGE.md](LANGUAGE.md) vocabulary 和 CONTEXT.md vocabulary，让每个 sub-agent 都能用 architecture language 和项目 domain language 一致命名事物。

每个 sub-agent 输出：

1. Interface（types、methods、params，以及 invariants、ordering、error modes）
2. 展示 callers 如何使用它的 usage example
3. Implementation 在 seam 后隐藏了什么
4. Dependency strategy 和 adapters（见 [DEEPENING.md](DEEPENING.md)）
5. Trade-offs：哪里 leverage 高，哪里薄

### 3. Present and compare

按顺序展示 designs，让用户能逐个吸收，然后用 prose 比较它们。按 **depth**（interface 上的 leverage）、**locality**（change 集中在哪里）和 **seam placement** 对比。

比较后给出你自己的 recommendation：你认为哪个 design 最强以及原因。如果不同 designs 的元素适合组合，提出 hybrid。要有主见：用户想要的是强判断，不是菜单。
