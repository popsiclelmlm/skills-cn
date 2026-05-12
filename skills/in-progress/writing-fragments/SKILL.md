---
name: writing-fragments
description: 一场 grilling session，从用户那里挖掘 fragments，即异质的写作 nuggets（claims、vignettes、sharp sentences、half-thoughts），并把它们追加到单个 document 中，作为未来文章的 raw material。当用户想在施加结构前发展 ideas，或提到 "fragments"、"ideate"、"raw material" 写作时使用。
---

<what-to-do>

运行一场会产出 fragments 的 grilling session。围绕用户想写的任何主题 relentlessly interview。不要施加 phases、outlines 或 structure：这明确 out of scope。

当 fragments 从对话任何一侧浮现，把它们追加到单个 markdown file。用户会在 session 期间编辑这个 file；每次写入前都重新读取，确保保留他们的 edits。

如果用户没有传 path，只问一次 document 保存到哪里，然后在本 session 剩余时间记住它。

从用户说的第一句话开始捕获 fragments，包括 initial prompt。

首次写入时，在顶部放一个带 working title 的单个 H1（之后可以改），除此之外什么都不加：没有 metadata、没有 TOC、没有 date。

</what-to-do>

<supporting-info>

## What is a fragment

Fragment 是任何可能存活到最终 article 中的 text。它必须_作者读得懂_：作者能知道它什么意思；但不需要定义术语，也不需要让冷读者理解。门槛是 “这是不是一段好写作？”，而不是 “这是不是一个自包含 argument？”

Fragments 有意保持异质。可能成为 fragment 的例子：

- 你想在某处使用、但还不知道放哪的一句 sharp sentence。
- 一个带一行 justification 的 claim。
- 一个 vignette：发生过的事、code snippet、scenario、analogy。
- 一个 half-thought："something about how X feels like Y, work this out later."
- 一个 quote、一段 dialogue、一句 overheard line。
- 一组凭感觉挂在一起的 related observations。
- 一段 complaint、confession、punchline。

Novelist's diary 是模型：多年无结构 noticings，之后被挖掘成 raw material。Fragments 就是 noticings。

## File format

```markdown
# Working title

A first fragment lives here.

It can be multiple paragraphs. It can include lists, code, quotes — whatever
shape the fragment naturally takes.

---

A second fragment.

---

> A quoted line that the user wants to keep around.

A reaction to it.

---

- A cluster of related observations
- That hang together by feel
- And want to be near each other
```

Fragments 用 horizontal rule（`\n---\n`）分隔。Body 中没有 headings。没有 tags。除了添加顺序之外没有 order。

## Writing rhythm

静默 append。不要为每个 fragment 请求 permission。顺口提一下你添加了什么（"adding that"），但不要用 save dialogs 打断对话。

每次写入前：从磁盘重新读取 file。用户可能在 turns 之间编辑、重排或删除 fragments；保留他们的 changes。永远不要 overwrite file；只 append（或者当用户要求时，原地编辑某个 specific fragment）。

用户可以随时说 "cut the last one"、"rewrite that one sharper"、"merge those two"。把这些视为 first-class instructions。

</supporting-info>
