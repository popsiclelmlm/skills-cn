---
name: writing-shape
description: 接收一个 raw material 的 markdown file，通过 conversational session 把它塑造成文章：起草 candidate openings，逐段增长文章，并在每一步争论 format（lists、tables、callouts、quotes）。当用户有一堆 notes、fragments 或 rough draft，并想把它变成可发布内容时使用。
---

<what-to-do>

用户已经传入（或将传入）一个 raw material 的 markdown file。把它视为 input pile：可以是整洁 fragments 列表、无结构 prose 墙、transcript，任何形式都可以。格式不重要。先从头到尾读取它，再做其他事。

然后运行一个 shaping session，产出一个单独的 article document。不要编辑 raw material file：对这个 skill 来说它是 read-only。

如果用户没有说明 article 保存位置，只问一次并记住 path。用户会在 session 期间编辑 article file；每次写入前都重新读取，确保保留他们的 edits。

</what-to-do>

<supporting-info>

## The loop

1. **Read the pile.** 完整读取 input file。形成对内容的感觉。
2. **Draft 2–3 candidate openings.** 每个 opening 都应该暗示文章不同的 thesis 或 angle。全部展示。迫使用户选择一个或组合 hybrid。选定 opening 会定义文章剩余部分必须完成什么。
3. **Grow paragraph by paragraph.** Opening 落定后，问 “given this opening, what does the reader need to hear next?” 从 pile 中抽取 material 来回答。争论 next beat 应该是 paragraph、list、table、callout、quote 还是 code block。每个 format choice 都应 deliberate 且 defensible。
4. **Append to the article file as you go.** 不要 batch。每个达成一致的 paragraph 或 block 都立即写入，让用户看到文章成形。
5. **Loop step 3 until the article is done.** 由用户决定何时完成。

## Conversational feel

这是反过来的 grilling session。在 ideation 中，问题是 “what are you actually noticing?” 这里是 “what is this article actually arguing, and in what order does the reader need to hear it?” 要 push back。拒绝放过 weak transitions。如果某段不配占位置，就 cut it。

持续使用这些 specific moves：

- "What does this paragraph do for the reader that the previous one didn't?"
- "If I cut this, what breaks?"
- "Is this prose, or should it be a list? Why prose?"
- "This sentence is doing two jobs — split it or pick one."
- "The opening promised X. We've drifted to Y. Either re-thread it or change the opening."

## Pulling from the pile

把 raw material 当成 quarry，而不是 script。抽取 fragment，重写到适配周围 paragraph，然后放置。一个 fragment 可以拆到多个 paragraphs 中、与另一个合并，或 paraphrase。Pile 的工作是被开采；article 的工作是读起来像一个 voice。

如果 pile 缺少 article 需要的东西，明确指出 gap："We need an example here and the pile doesn't have one — give me one now or we cut this section."

## Format arguments to actually have

选择如何 render 一个 beat 时，和用户大声权衡这些 tradeoffs，而不是默默决定：

- **Prose vs. list.** Prose 承载 argument；lists 承载 parallel items。如果 items 不是真正 parallel，prose 更好。如果是，list 更易扫描。
- **Inline vs. callout.** Tips、warnings 和 asides 放进 callouts（`> [!TIP]`、`> [!NOTE]`），但只有当它们 inline 时确实会 derail main argument 才这样做。否则留在 inline。
- **Table vs. repeated structure.** 如果同一种 shape 以相同 fields 重复 3+ 次，用 table。否则用带 bold leads 的 prose。
- **Quote vs. paraphrase.** 当原始措辞本身是重点时 quote。只有 idea 重要时 paraphrase。
- **Code block vs. inline code.** Multi-line、runnable 或 illustrative → block。Single token 或 identifier → inline。

## Writing rhythm

每个 block 达成一致后 append 到 article file。每次写入前从磁盘重新读取 file，因为用户可能在 turns 之间编辑。永远不要盲目 overwrite。如果用户想重写某段，原地编辑那一段，其余保持不动。

## Out of scope

- 挖掘 pile 中不存在的新 fragments（pile 是输入；如果不完整，指出 gap，并让用户补充或 cut 该 section）。
- 编辑 raw material file。
- Publishing、为特定 platform 格式化，或添加用户没有要求的 frontmatter。

</supporting-info>
