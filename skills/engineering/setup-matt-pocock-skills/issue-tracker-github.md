# Issue tracker: GitHub

此 repo 的 Issues 和 PRDs 位于 GitHub issues。所有操作使用 `gh` CLI。

## Conventions

- **Create an issue**：`gh issue create --title "..." --body "..."`。多行 bodies 使用 heredoc。
- **Read an issue**：`gh issue view <number> --comments`，用 `jq` 过滤 comments，同时获取 labels。
- **List issues**：`gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`，并使用合适的 `--label` 和 `--state` filters。
- **Comment on an issue**：`gh issue comment <number> --body "..."`
- **Apply / remove labels**：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **Close**：`gh issue close <number> --comment "..."`

从 `git remote -v` 推断 repo；`gh` 在 clone 内运行时会自动完成。

## When a skill says "publish to the issue tracker"

创建一个 GitHub issue。

## When a skill says "fetch the relevant ticket"

运行 `gh issue view <number> --comments`。
