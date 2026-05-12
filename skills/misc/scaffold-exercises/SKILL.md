---
name: scaffold-exercises
description: 创建能通过 linting 的 exercise directory structures，包含 sections、problems、solutions 和 explainers。当用户想 scaffold exercises、创建 exercise stubs，或设置新的 course section 时使用。
---

# Scaffold Exercises

创建能通过 `pnpm ai-hero-cli internal lint` 的 exercise directory structures，然后用 `git commit` 提交。

## Directory naming

- **Sections**：`exercises/` 内的 `XX-section-name/`（例如 `01-retrieval-skill-building`）
- **Exercises**：section 内的 `XX.YY-exercise-name/`（例如 `01.03-retrieval-with-bm25`）
- Section number = `XX`，exercise number = `XX.YY`
- Names 使用 dash-case（小写、hyphens）

## Exercise variants

每个 exercise 至少需要以下 subfolders 之一：

- `problem/` — 带 TODOs 的 student workspace
- `solution/` — reference implementation
- `explainer/` — conceptual material，没有 TODOs

Stub 时默认使用 `explainer/`，除非 plan 明确另有说明。

## Required files

每个 subfolder（`problem/`、`solution/`、`explainer/`）都需要一个 `readme.md`，并且：

- **不为空**（必须有真实内容，哪怕只有一行 title 也可以）
- 没有 broken links

Stub 时，创建带 title 和 description 的最小 readme：

```md
# Exercise Title

Description here
```

如果 subfolder 有 code，它还需要一个 `main.ts`（>1 行）。但对 stubs 来说，只有 readme 的 exercise 也可以。

## Workflow

1. **Parse the plan** — 提取 section names、exercise names 和 variant types
2. **Create directories** — 对每个 path 使用 `mkdir -p`
3. **Create stub readmes** — 每个 variant folder 一个带 title 的 `readme.md`
4. **Run lint** — 使用 `pnpm ai-hero-cli internal lint` 验证
5. **Fix any errors** — 迭代直到 lint 通过

## Lint rules summary

Linter（`pnpm ai-hero-cli internal lint`）会检查：

- 每个 exercise 都有 subfolders（`problem/`、`solution/`、`explainer/`）
- 至少存在 `problem/`、`explainer/` 或 `explainer.1/` 之一
- primary subfolder 中存在且非空的 `readme.md`
- 没有 `.gitkeep` files
- 没有 `speaker-notes.md` files
- readmes 中没有 broken links
- readmes 中没有 `pnpm run exercise` commands
- 每个 subfolder 都需要 `main.ts`，除非它是 readme-only

## Moving/renaming exercises

重新编号或移动 exercises 时：

1. 使用 `git mv`（不是 `mv`）重命名 directories，以保留 git history
2. 更新 numeric prefix 以保持顺序
3. 移动后重新运行 lint

Example:

```bash
git mv exercises/01-retrieval/01.03-embeddings exercises/01-retrieval/01.04-embeddings
```

## Example: stubbing from a plan

给定这样的 plan：

```
Section 05: Memory Skill Building
- 05.01 Introduction to Memory
- 05.02 Short-term Memory (explainer + problem + solution)
- 05.03 Long-term Memory
```

创建：

```bash
mkdir -p exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer
mkdir -p exercises/05-memory-skill-building/05.02-short-term-memory/{explainer,problem,solution}
mkdir -p exercises/05-memory-skill-building/05.03-long-term-memory/explainer
```

然后创建 readme stubs：

```
exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer/readme.md -> "# Introduction to Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/explainer/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/problem/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/solution/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.03-long-term-memory/explainer/readme.md -> "# Long-term Memory"
```
