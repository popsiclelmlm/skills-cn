---
name: handoff
description: 将当前对话压缩成 handoff document，供另一个 agent 接手。
argument-hint: "What will the next session be used for?"
---

写一份 handoff document，总结当前对话，让一个全新的 agent 可以继续工作。把它保存到由 `mktemp -t handoff-XXXXXX.md` 生成的路径（写入前先读取该文件）。

建议下一次 session 应使用的 skills（如有）。

不要重复已经记录在其他 artifacts（PRDs、plans、ADRs、issues、commits、diffs）中的内容。改用 path 或 URL 引用它们。

如果用户传入了 arguments，把它们视为下一次 session 将聚焦内容的描述，并据此调整文档。
