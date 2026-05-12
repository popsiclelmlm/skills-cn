---
name: writing-beats
description: 以 beats 旅程的方式塑造文章，类似 choose-your-own-adventure。用户从 raw material 中选择一个 starting beat，你只写那个 beat，然后提供下一步 pivot 选项，逐 beat 推进，直到文章自然结束。当用户有 raw material，并想把它组装成 narrative 而不是 argument 时使用。
---

<what-to-do>

用户已经传入（或将传入）一个 raw material 的 markdown file。

如果用户没有说明 article 保存位置，只问一次并记住 path。

然后运行 beat-by-beat journey：

1. 从 raw material 中写 2–3 个 candidate **starting beats**。每个都是进入文章的不同入口。写入 article file 前先展示给用户。用户选择一个。预览写完后可能通向哪些 beats，像是让用户稍微看到路的前方。
2. 用户选择 starting beat 后，**只把那个 beat** 写入 article file。一个 beat 可以是一句话，也可以是几段：由那个 beat 自然需要的形状决定。到此停止。
3. 从磁盘重新读取 article file。然后提供 2–3 个 candidate **next beats**：从文章当前状态出发可 pivot 的不同方向。
4. 循环 steps 2–4，直到文章自然结束。

</what-to-do>

<supporting-info>

## What is a beat

Beat 是旅程中的一个 move。它只做一件事：设置场景、落下观点、提出问题、插入 aside、扭转 angle。然后它停下，把读者留在下一个 beat 可以 pivot 的地方。

Beat 的大小由它需要什么决定：

- 如果 move 本身就只有一句话，那就是一句话（"And then nothing happened for three weeks."）。
- 如果 move 需要 setup，就是短段落。
- 如果 beat 是自包含的 vignette、argument 或 example，可以是多段。

如果一个 “beat” 需要五段和三个 subheadings，它就不是 beat，而是两个 beats 粘在一起。拆开。

## Writing one beat

一旦选定 beat，只把_那个 beat_写入 article file。不要写下一个 beat。

从 raw pile 中抽取材料填充 beat。你可以 paraphrase、split、recombine 或 quote。Pile 是 quarry。

## Ending the journey

文章在 journey 完整时结束，而不是 pile 为空时结束。大多数 piles 会有没用上的 leftover fragments。这没关系；这正是拥有超过所需 raw material 的意义。

## Writing rhythm

- 一次 append 一个 beat。永远不要提前写。
- 每次写入前都从磁盘重新读取 article file。绝对保留用户 edits。
- 如果用户大幅编辑了之前的 beat，让它改变接下来会发生什么。
- 如果用户说 "rewrite that beat" 或 "go back and try a different beat 3"，照做：原地编辑，只动对应内容。

</supporting-info>
