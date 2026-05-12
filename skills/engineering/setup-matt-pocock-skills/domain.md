# Domain Docs

Engineering skills 在探索 codebase 时应如何消费此 repo 的 domain documentation。

## Before exploring, read these

- repo root 下的 **`CONTEXT.md`**，或
- 如果存在，则读取 repo root 下的 **`CONTEXT-MAP.md`**。它指向每个 context 的一个 `CONTEXT.md`。读取与 topic 相关的每一个。
- **`docs/adr/`** — 读取与你即将处理区域相关的 ADRs。在 multi-context repos 中，也检查 `src/<context>/docs/adr/` 中 context-scoped decisions。

如果这些 files 不存在，**静默继续**。不要标记它们缺失；不要提前建议创建。Producer skill（`/grill-with-docs`）会在 terms 或 decisions 确实被解决时惰性创建它们。

## File structure

Single-context repo（大多数 repos）：

```
/
├── CONTEXT.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

Multi-context repo（root 下存在 `CONTEXT-MAP.md`）：

```
/
├── CONTEXT-MAP.md
├── docs/adr/                          ← system-wide decisions
└── src/
    ├── ordering/
    │   ├── CONTEXT.md
    │   └── docs/adr/                  ← context-specific decisions
    └── billing/
        ├── CONTEXT.md
        └── docs/adr/
```

## Use the glossary's vocabulary

当你的 output 命名 domain concept（issue title、refactor proposal、hypothesis、test name 中）时，使用 `CONTEXT.md` 中定义的 term。不要漂移到 glossary 明确避免的 synonyms。

如果你需要的 concept 还不在 glossary 中，这是一个信号：要么你在发明项目不用的语言（重新考虑），要么确实存在 gap（为 `/grill-with-docs` 记录下来）。

## Flag ADR conflicts

如果你的 output 与现有 ADR 冲突，显式展示它，而不是静默覆盖：

> _Contradicts ADR-0007 (event-sourced orders) — but worth reopening because…_
