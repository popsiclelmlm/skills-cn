---
name: review
description: 沿两个轴 review 从固定点（commit、branch、tag 或 merge-base）以来的变更：Standards（code 是否遵循此 repo 已记录的 coding standards？）和 Spec（code 是否匹配来源 issue/PRD 的要求？）。在并行 sub-agents 中运行两个 reviews，并并排报告。当用户想 review branch、PR、work-in-progress changes，或要求 "review since X" 时使用。
---

# Review

对 `HEAD` 和用户提供的固定点之间的 diff 做双轴 review：

- **Standards** — code 是否符合此 repo 已记录的 coding standards？
- **Spec** — code 是否忠实实现了来源 issue / PRD / spec？

两个轴都作为 **parallel sub-agents** 运行，避免彼此污染 context，然后此 skill 聚合它们的 findings。

Issue tracker 应该已经提供给你；如果缺少 `docs/agents/issue-tracker.md`，运行 `/setup-matt-pocock-skills`。

## Process

### 1. Pin the fixed point

用户说的任何东西都是 fixed point：commit SHA、branch name、tag、`main`、`HEAD~5` 等。不要有自己的偏好，直接传递。如果他们没有指定，询问："Review against what — a branch, a commit, or `main`?" 拿到 fixed point 前不要继续。

记录一次 diff command：`git diff <fixed-point>...HEAD`（three-dot，所以比较对象是 merge-base）。同时用 `git log <fixed-point>..HEAD --oneline` 记录 commits list。

### 2. Identify the spec source

按以下顺序寻找 originating spec：

1. Commit messages 中的 issue references（`#123`、`Closes #45`、GitLab `!67` 等）— 通过 `docs/agents/issue-tracker.md` 中的 workflow 获取。
2. 用户作为 argument 传入的 path。
3. `docs/`、`specs/` 或 `.scratch/` 下匹配 branch name 或 feature 的 PRD/spec file。
4. 如果什么都没找到，询问用户 spec 在哪里。如果他们说没有，**Spec** sub-agent 会跳过并报告 "no spec available"。

### 3. Identify the standards sources

Repo 中任何记录 code 应如何编写的东西。常见位置：

- `CLAUDE.md`、`AGENTS.md`
- `CONTRIBUTING.md`
- `CONTEXT.md`、`CONTEXT-MAP.md`、per-context `CONTEXT.md` files
- `docs/adr/`（architectural decisions 也是 standards）
- `.editorconfig`、`eslint.config.*`、`biome.json`、`prettier.config.*`、`tsconfig.json`（machine-enforced standards：记录它们，但不要重复检查 tooling 已检查的内容）
- repo root 或 `docs/` 下任何 `STYLE.md`、`STANDARDS.md`、`STYLEGUIDE.md` 或类似文件

收集 files list。**Standards** sub-agent 会读取它们。

### 4. Spawn both sub-agents in parallel

发送一条包含两个 `Agent` tool calls 的 message。两者都使用 `general-purpose` subagent。

**Standards sub-agent prompt** — 包含：

- 完整 diff command 和 commit list。
- Step 3 找到的 standards-source files list。
- Brief："Read the standards docs. Then read the diff. Report — per file/hunk where relevant — every place the diff violates a documented standard. Cite the standard (file + the rule). Distinguish hard violations from judgement calls. Skip anything tooling enforces. Under 400 words."

**Spec sub-agent prompt** — 包含：

- Diff command 和 commit list。
- Spec 的 path 或 fetched contents。
- Brief："Read the spec. Then read the diff. Report: (a) requirements the spec asked for that are missing or partial; (b) behaviour in the diff that wasn't asked for (scope creep); (c) requirements that look implemented but where the implementation looks wrong. Quote the spec line for each finding. Under 400 words."

如果 spec 缺失，跳过 Spec sub-agent，并在 final report 中说明。

### 5. Aggregate

在 `## Standards` 和 `## Spec` headings 下展示两个 reports，逐字或轻微清理。不要 merge 或 rerank findings：两个轴刻意分开，方便用户独立查看。

以一句 summary 收尾：每个轴的 findings 总数，以及最严重的单个 issue（如有）。

## Why two axes

一个 change 可能通过一个轴而失败在另一个轴：

- Code 遵循所有 standards，但实现了错误东西 → **Standards pass, Spec fail.**
- Code 准确完成 issue 要求，但破坏了项目 conventions → **Spec pass, Standards fail.**

分开报告能防止一个轴遮蔽另一个轴。
