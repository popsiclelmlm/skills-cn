---
name: setup-matt-pocock-skills
description: 在 AGENTS.md/CLAUDE.md 和 `docs/agents/` 中设置 `## Agent skills` block，让 engineering skills 知道该 repo 的 issue tracker（GitHub 或 local markdown）、triage label vocabulary 和 domain doc layout。在第一次使用 `to-issues`、`to-prd`、`triage`、`diagnose`、`tdd`、`improve-codebase-architecture` 或 `zoom-out` 前运行；如果这些 skills 看起来缺少 issue tracker、triage labels 或 domain docs 上下文，也运行它。
disable-model-invocation: true
---

# Setup Matt Pocock's Skills

Scaffold engineering skills 假设存在的每仓库配置：

- **Issue tracker** — issues 所在位置（默认 GitHub；local markdown 也开箱支持）
- **Triage labels** — 五个 canonical triage roles 使用的 strings
- **Domain docs** — `CONTEXT.md` 和 ADRs 所在位置，以及读取它们的 consumer rules

这是一个 prompt-driven skill，不是 deterministic script。先探索，展示发现，与用户确认，然后写入。

## Process

### 1. Explore

查看当前 repo，理解它的起始状态。读取已经存在的东西；不要假设：

- `git remote -v` 和 `.git/config` — 这是 GitHub repo 吗？是哪一个？
- repo root 下的 `AGENTS.md` 和 `CLAUDE.md` — 是否存在？其中是否已经有 `## Agent skills` section？
- repo root 下的 `CONTEXT.md` 和 `CONTEXT-MAP.md`
- `docs/adr/` 以及任何 `src/*/docs/adr/` directories
- `docs/agents/` — 这个 skill 之前的输出是否已经存在？
- `.scratch/` — local-markdown issue tracker convention 已在使用的信号

### 2. Present findings and ask

总结已有内容和缺失内容。然后带用户逐一完成三个 decisions：**一次一个**。展示一个 section，得到用户答案，然后进入下一个。不要一次性倾倒三个问题。

假设用户不知道这些术语是什么意思。每个 section 都用短 explainer 开头（它是什么、为什么这些 skills 需要它、不同选择会改变什么）。然后展示 choices 和 default。

**Section A — Issue tracker.**

> Explainer: The "issue tracker" is where issues live for this repo. Skills like `to-issues`, `triage`, `to-prd`, and `qa` read from and write to it — they need to know whether to call `gh issue create`, write a markdown file under `.scratch/`, or follow some other workflow you describe. Pick the place you actually track work for this repo.

Default posture：这些 skills 是为 GitHub 设计的。如果 `git remote` 指向 GitHub，就建议 GitHub。如果 `git remote` 指向 GitLab（`gitlab.com` 或 self-hosted host），就建议 GitLab。否则（或如果用户更喜欢），提供：

- **GitHub** — issues 位于 repo 的 GitHub Issues（使用 `gh` CLI）
- **GitLab** — issues 位于 repo 的 GitLab Issues（使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI）
- **Local markdown** — issues 作为文件存在于此 repo 的 `.scratch/<feature>/` 下（适合 solo projects 或没有 remote 的 repos）
- **Other**（Jira、Linear 等）— 让用户用一段话描述 workflow；skill 会把它记录为 freeform prose

**Section B — Triage label vocabulary.**

> Explainer: When the `triage` skill processes an incoming issue, it moves it through a state machine — needs evaluation, waiting on reporter, ready for an AFK agent to pick up, ready for a human, or won't fix. To do that, it needs to apply labels (or the equivalent in your issue tracker) that match strings *you've actually configured*. If your repo already uses different label names (e.g. `bug:triage` instead of `needs-triage`), map them here so the skill applies the right ones instead of creating duplicates.

五个 canonical roles：

- `needs-triage` — maintainer 需要评估
- `needs-info` — 等待 reporter 提供更多信息
- `ready-for-agent` — 完全规格化，AFK-ready（agent 可以在没有 human context 的情况下接手）
- `ready-for-human` — 需要 human implementation
- `wontfix` — 不会处理

Default：每个 role 的 string 等于它的 name。询问用户是否想 override 任何项。如果他们的 issue tracker 没有现有 labels，defaults 就可以。

**Section C — Domain docs.**

> Explainer: Some skills (`improve-codebase-architecture`, `diagnose`, `tdd`) read a `CONTEXT.md` file to learn the project's domain language, and `docs/adr/` for past architectural decisions. They need to know whether the repo has one global context or multiple (e.g. a monorepo with separate frontend/backend contexts) so they look in the right place.

确认 layout：

- **Single-context** — repo root 下一个 `CONTEXT.md` + `docs/adr/`。大多数 repos 都是这样。
- **Multi-context** — root 下 `CONTEXT-MAP.md` 指向 per-context `CONTEXT.md` files（通常是 monorepo）。

### 3. Confirm and edit

向用户展示 draft：

- 要添加到正在编辑的 `CLAUDE.md` / `AGENTS.md` 中的 `## Agent skills` block（选择规则见 step 4）
- `docs/agents/issue-tracker.md`、`docs/agents/triage-labels.md`、`docs/agents/domain.md` 的内容

写入前让他们编辑。

### 4. Write

**选择要编辑的文件：**

- 如果 `CLAUDE.md` 存在，编辑它。
- 否则如果 `AGENTS.md` 存在，编辑它。
- 如果两者都不存在，询问用户要创建哪一个，不要替他们选择。

当 `CLAUDE.md` 已存在时，绝不要创建 `AGENTS.md`（反之亦然）；始终编辑已经存在的那个。

如果选中的文件中已经存在 `## Agent skills` block，就原地更新其内容，而不是追加 duplicate。不要覆盖周围 sections 中的用户 edits。

Block:

```markdown
## Agent skills

### Issue tracker

[one-line summary of where issues are tracked]. See `docs/agents/issue-tracker.md`.

### Triage labels

[one-line summary of the label vocabulary]. See `docs/agents/triage-labels.md`.

### Domain docs

[one-line summary of layout — "single-context" or "multi-context"]. See `docs/agents/domain.md`.
```

然后用这个 skill folder 中的 seed templates 作为起点，写三个 docs files：

- [issue-tracker-github.md](./issue-tracker-github.md) — GitHub issue tracker
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md) — GitLab issue tracker
- [issue-tracker-local.md](./issue-tracker-local.md) — local-markdown issue tracker
- [triage-labels.md](./triage-labels.md) — label mapping
- [domain.md](./domain.md) — domain doc consumer rules + layout

对 “other” issue trackers，使用用户描述从零写 `docs/agents/issue-tracker.md`。

### 5. Done

告诉用户 setup 已完成，以及哪些 engineering skills 现在会读取这些 files。提到他们之后可以直接编辑 `docs/agents/*.md`；只有当他们想切换 issue trackers 或从头重来时，才需要重新运行这个 skill。
