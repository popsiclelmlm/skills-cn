# ADR Format

ADRs 存放在 `docs/adr/`，并使用 sequential numbering：`0001-slug.md`、`0002-slug.md` 等。

惰性创建 `docs/adr/` directory：只有在第一个 ADR 被需要时才创建。

## Template

```md
# {Short title of the decision}

{1-3 sentences: what's the context, what did we decide, and why.}
```

就是这样。一个 ADR 可以只有一段。价值在于记录_某个_ decision 已经做出，以及_为什么_做出，而不是填满各个 sections。

## Optional sections

只有在它们提供真实价值时才包含。大多数 ADRs 不需要它们。

- **Status** frontmatter（`proposed | accepted | deprecated | superseded by ADR-NNNN`）— 当 decisions 被重新审视时很有用
- **Considered Options** — 只有当被拒绝的 alternatives 值得记住时
- **Consequences** — 只有当非显然的 downstream effects 需要被点明时

## Numbering

扫描 `docs/adr/` 中的最高现有 number，并加一。

## When to offer an ADR

以下三点必须全部为真：

1. **Hard to reverse** — 之后改变主意的成本有意义
2. **Surprising without context** — 未来读者看着 code 会疑惑 “why on earth did they do it this way?”
3. **The result of a real trade-off** — 曾有真正 alternatives，并且你基于具体原因选择了其中一个

如果一个 decision 很容易反转，跳过它，因为你会直接反转。如果它不令人惊讶，没人会问为什么。如果没有真实 alternative，除了 “we did the obvious thing” 之外没有什么可记录。

### What qualifies

- **Architectural shape.** “We're using a monorepo.” “The write model is event-sourced, the read model is projected into Postgres.”
- **Integration patterns between contexts.** “Ordering and Billing communicate via domain events, not synchronous HTTP.”
- **Technology choices that carry lock-in.** Database、message bus、auth provider、deployment target。不是每个 library，只是那些换掉要花一个 quarter 的东西。
- **Boundary and scope decisions.** “Customer data is owned by the Customer context; other contexts reference it by ID only.” 明确的 no 和 yes 一样有价值。
- **Deliberate deviations from the obvious path.** “We're using manual SQL instead of an ORM because X.” 任何合理读者会默认相反做法的地方。这些会阻止下一位 engineer 去 “fix” 某个故意为之的东西。
- **Constraints not visible in the code.** “We can't use AWS because of compliance requirements.” “Response times must be under 200ms because of the partner API contract.”
- **Rejected alternatives when the rejection is non-obvious.** 如果你考虑过 GraphQL，并因为微妙原因选择了 REST，记录它，否则六个月后又会有人建议 GraphQL。
