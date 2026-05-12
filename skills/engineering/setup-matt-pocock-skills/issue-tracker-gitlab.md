# Issue tracker: GitLab

此 repo 的 Issues 和 PRDs 位于 GitLab issues。所有操作使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI。

## Conventions

- **Create an issue**：`glab issue create --title "..." --description "..."`。多行 descriptions 使用 heredoc。传 `--description -` 会打开 editor。
- **Read an issue**：`glab issue view <number> --comments`。使用 `-F json` 获得 machine-readable output。
- **List issues**：`glab issue list -F json`，并带上合适的 `--label` filters。
- **Comment on an issue**：`glab issue note <number> --message "..."`。GitLab 把 comments 称为 "notes"。
- **Apply / remove labels**：`glab issue update <number> --label "..."` / `--unlabel "..."`。多个 labels 可以用逗号分隔，也可以重复 flag。
- **Close**：`glab issue close <number>`。`glab issue close` 不接受 closing comment，所以先用 `glab issue note <number> --message "..."` 发布 explanation，然后 close。
- **Merge requests**：GitLab 把 PRs 称为 "merge requests"。使用 `glab mr create`、`glab mr view`、`glab mr note` 等；形状与 `gh pr ...` 相同，但用 `mr` 替代 `pr`，用 `note`/`--message` 替代 `comment`/`--body`。

从 `git remote -v` 推断 repo；`glab` 在 clone 内运行时会自动完成。

## When a skill says "publish to the issue tracker"

创建一个 GitLab issue。

## When a skill says "fetch the relevant ticket"

运行 `glab issue view <number> --comments`。
