---
name: ubiquitous-language
description: 从当前 conversation 中提取 DDD-style ubiquitous language glossary，标记 ambiguities 并提出 canonical terms。保存到 UBIQUITOUS_LANGUAGE.md。当用户想定义 domain terms、构建 glossary、强化 terminology、创建 ubiquitous language，或提到 "domain model" 或 "DDD" 时使用。
disable-model-invocation: true
---

# Ubiquitous Language

从当前 conversation 中提取并 formalize domain terminology，形成一致 glossary，并保存到本地文件。

## Process

1. **Scan the conversation**，寻找 domain-relevant nouns、verbs 和 concepts
2. **Identify problems**：
   - 同一个 word 用于不同 concepts（ambiguity）
   - 不同 words 用于同一个 concept（synonyms）
   - 模糊或 overloaded terms
3. **Propose a canonical glossary**，给出有主见的 term choices
4. **Write to `UBIQUITOUS_LANGUAGE.md`**，在 working directory 中使用下面格式写入
5. **Output a summary**，在对话中 inline 输出

## Output Format

写一个 `UBIQUITOUS_LANGUAGE.md` file，结构如下：

```md
# Ubiquitous Language

## Order lifecycle

| Term        | Definition                                              | Aliases to avoid      |
| ----------- | ------------------------------------------------------- | --------------------- |
| **Order**   | A customer's request to purchase one or more items      | Purchase, transaction |
| **Invoice** | A request for payment sent to a customer after delivery | Bill, payment request |

## People

| Term         | Definition                                  | Aliases to avoid       |
| ------------ | ------------------------------------------- | ---------------------- |
| **Customer** | A person or organization that places orders | Client, buyer, account |
| **User**     | An authentication identity in the system    | Login, account         |

## Relationships

- An **Invoice** belongs to exactly one **Customer**
- An **Order** produces one or more **Invoices**

## Example dialogue

> **Dev:** "When a **Customer** places an **Order**, do we create the **Invoice** immediately?"
> **Domain expert:** "No — an **Invoice** is only generated once a **Fulfillment** is confirmed. A single **Order** can produce multiple **Invoices** if items ship in separate **Shipments**."
> **Dev:** "So if a **Shipment** is cancelled before dispatch, no **Invoice** exists for it?"
> **Domain expert:** "Exactly. The **Invoice** lifecycle is tied to the **Fulfillment**, not the **Order**."

## Flagged ambiguities

- "account" was used to mean both **Customer** and **User** — these are distinct concepts: a **Customer** places orders, while a **User** is an authentication identity that may or may not represent a **Customer**.
```

## Rules

- **Be opinionated.** 当同一 concept 有多个词时，选择最佳词，并把其他词列为要避免的 aliases。
- **Flag conflicts explicitly.** 如果某个 term 在 conversation 中被模糊使用，在 "Flagged ambiguities" section 中指出，并给出清晰 recommendation。
- **Only include terms relevant for domain experts.** 跳过 module 或 class 名称，除非它们在 domain language 中有意义。
- **Keep definitions tight.** 最多一句话。定义它是什么，而不是它做什么。
- **Show relationships.** 使用加粗 term names，并在明显时表达 cardinality。
- **Only include domain terms.** 跳过 generic programming concepts（array、function、endpoint），除非它们有 domain-specific meaning。
- **Group terms into multiple tables**，当自然 clusters 出现时（例如按 subdomain、lifecycle 或 actor）。每组都有自己的 heading 和 table。如果所有 terms 属于单一 cohesive domain，一个 table 就可以，不要强行分组。
- **Write an example dialogue.** 写一段 dev 和 domain expert 之间的短对话（3-5 exchanges），展示 terms 如何自然交互。Dialogue 应澄清 related concepts 之间的 boundaries，并展示 terms 被精确使用。

<example>

## Example dialogue

> **Dev:** "How do I test the **sync service** without Docker?"

> **Domain expert:** "Provide the **filesystem layer** instead of the **Docker layer**. It implements the same **Sandbox service** interface but uses a local directory as the **sandbox**."

> **Dev:** "So **sync-in** still creates a **bundle** and unpacks it?"

> **Domain expert:** "Exactly. The **sync service** doesn't know which layer it's talking to. It calls `exec` and `copyIn` — the **filesystem layer** just runs those as local shell commands."

</example>

## Re-running

在同一 conversation 中再次调用时：

1. 读取现有 `UBIQUITOUS_LANGUAGE.md`
2. 合并 subsequent discussion 中的任何 new terms
3. 如果理解已演变，更新 definitions
4. 重新标记任何 new ambiguities
5. 重写 example dialogue，以包含 new terms
