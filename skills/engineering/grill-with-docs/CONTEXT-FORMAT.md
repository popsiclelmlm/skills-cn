# CONTEXT.md Format

## Structure

```md
# {Context Name}

{One or two sentence description of what this context is and why it exists.}

## Language

**Order**:
{A concise description of the term}
_Avoid_: Purchase, transaction

**Invoice**:
A request for payment sent to a customer after delivery.
_Avoid_: Bill, payment request

**Customer**:
A person or organization that places orders.
_Avoid_: Client, buyer, account

## Relationships

- An **Order** produces one or more **Invoices**
- An **Invoice** belongs to exactly one **Customer**

## Example dialogue

> **Dev:** "When a **Customer** places an **Order**, do we create the **Invoice** immediately?"
> **Domain expert:** "No — an **Invoice** is only generated once a **Fulfillment** is confirmed."

## Flagged ambiguities

- "account" was used to mean both **Customer** and **User** — resolved: these are distinct concepts.
```

## Rules

- **Be opinionated.** 当同一 concept 有多个词时，选择最佳词，并把其他词列为要避免的 aliases。
- **Flag conflicts explicitly.** 如果一个 term 被模糊使用，在 "Flagged ambiguities" 中明确指出，并给出清晰 resolution。
- **Keep definitions tight.** 最多一句话。定义它是什么，而不是它做什么。
- **Show relationships.** 使用加粗 term names，并在明显时表达 cardinality。
- **只包含该项目 context 特有的 terms。** General programming concepts（timeouts、error types、utility patterns）不属于这里，即使项目广泛使用它们。添加 term 前，先问：这是该 context 独有的 concept，还是 general programming concept？只有前者属于这里。
- **Group terms under subheadings**，当自然 clusters 出现时。如果所有 terms 属于单一 cohesive area，扁平列表也可以。
- **Write an example dialogue.** 写一段 dev 和 domain expert 之间的对话，展示这些 terms 如何自然交互，并澄清相关 concepts 的 boundaries。

## Single vs multi-context repos

**Single context（大多数 repos）：** repo root 下一个 `CONTEXT.md`。

**Multiple contexts：** repo root 下一个 `CONTEXT-MAP.md`，列出 contexts、它们所在位置，以及彼此关系：

```md
# Context Map

## Contexts

- [Ordering](./src/ordering/CONTEXT.md) — receives and tracks customer orders
- [Billing](./src/billing/CONTEXT.md) — generates invoices and processes payments
- [Fulfillment](./src/fulfillment/CONTEXT.md) — manages warehouse picking and shipping

## Relationships

- **Ordering → Fulfillment**: Ordering emits `OrderPlaced` events; Fulfillment consumes them to start picking
- **Fulfillment → Billing**: Fulfillment emits `ShipmentDispatched` events; Billing consumes them to generate invoices
- **Ordering ↔ Billing**: Shared types for `CustomerId` and `Money`
```

Skill 会推断适用哪种结构：

- 如果 `CONTEXT-MAP.md` 存在，读取它来查找 contexts
- 如果只有 root `CONTEXT.md` 存在，则是 single context
- 如果两者都不存在，在第一个 term 被解决时惰性创建 root `CONTEXT.md`

当存在多个 contexts 时，推断当前 topic 关联哪一个。如果不清楚，就询问。
