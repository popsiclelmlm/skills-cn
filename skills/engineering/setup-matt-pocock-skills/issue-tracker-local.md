# Issue tracker: Local Markdown

此 repo 的 Issues 和 PRDs 作为 markdown files 位于 `.scratch/`。

## Conventions

- 每个 feature 一个 directory：`.scratch/<feature-slug>/`
- PRD 是 `.scratch/<feature-slug>/PRD.md`
- Implementation issues 是 `.scratch/<feature-slug>/issues/<NN>-<slug>.md`，从 `01` 开始编号
- Triage state 记录为每个 issue file 顶部附近的一行 `Status:`（role strings 见 `triage-labels.md`）
- Comments 和 conversation history 追加到文件底部的 `## Comments` heading 下

## When a skill says "publish to the issue tracker"

在 `.scratch/<feature-slug>/` 下创建新文件（需要时创建 directory）。

## When a skill says "fetch the relevant ticket"

读取 referenced path 上的文件。用户通常会直接传 path 或 issue number。
