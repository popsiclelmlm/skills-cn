# Out-of-Scope Knowledge Base

Repo 中的 `.out-of-scope/` directory 存储 rejected feature requests 的持久记录。它有两个目的：

1. **Institutional memory** — 记录 feature 为什么被拒绝，这样 issue 关闭后 reasoning 不会丢失
2. **Deduplication** — 当新 issue 与 prior rejection 匹配时，skill 可以展示之前的 decision，而不是重新争论

## Directory structure

```
.out-of-scope/
├── dark-mode.md
├── plugin-system.md
└── graphql-api.md
```

每个 **concept** 一个 file，不是每个 issue 一个 file。请求同一件事的多个 issues 归到一个 file 下。

## File format

文件应以轻松、可读的风格编写，更像短 design document，而不是 database entry。使用 paragraphs、code samples 和 examples，让第一次看到它的人也能理解 reasoning。

```markdown
# Dark Mode

This project does not support dark mode or user-facing theming.

## Why this is out of scope

The rendering pipeline assumes a single color palette defined in
`ThemeConfig`. Supporting multiple themes would require:

- A theme context provider wrapping the entire component tree
- Per-component theme-aware style resolution
- A persistence layer for user theme preferences

This is a significant architectural change that doesn't align with the
project's focus on content authoring. Theming is a concern for downstream
consumers who embed or redistribute the output.

```ts
// The current ThemeConfig interface is not designed for runtime switching:
interface ThemeConfig {
  colors: ColorPalette; // single palette, resolved at build time
  fonts: FontStack;
}
```

## Prior requests

- #42 — "Add dark mode support"
- #87 — "Night theme for accessibility"
- #134 — "Dark theme option"
```

### Naming the file

为 concept 使用简短、描述性的 kebab-case 名称：`dark-mode.md`、`plugin-system.md`、`graphql-api.md`。文件名应足够可识别，让浏览 directory 的人无需打开文件就能理解被拒绝的内容。

### Writing the reason

Reason 应该实质充分，不是 “we don't want this”，而是为什么。好的 reasons 会引用：

- Project scope 或 philosophy（“This project focuses on X; theming is a downstream concern”）
- Technical constraints（“Supporting this would require Y, which conflicts with our Z architecture”）
- Strategic decisions（“We chose to use A instead of B because...”）

Reason 应该 durable。避免引用临时情况（“we're too busy right now”）：这些不是真正 rejection，而是 deferrals。

## When to check `.out-of-scope/`

Triage 期间（Step 1: Gather context）读取 `.out-of-scope/` 中的所有 files。评估新 issue 时：

- 检查 request 是否匹配现有 out-of-scope concept
- Matching 基于 concept similarity，而不是 keyword；“night theme” 匹配 `dark-mode.md`
- 如果匹配，展示给 maintainer："This is similar to `.out-of-scope/dark-mode.md` — we rejected this before because [reason]. Do you still feel the same way?"

Maintainer 可以：

- **Confirm** — 新 issue 被添加到现有 file 的 "Prior requests" list，然后 close
- **Reconsider** — out-of-scope file 被删除或更新，issue 进入正常 triage
- **Disagree** — issues 相关但不同，继续正常 triage

## When to write to `.out-of-scope/`

只有当 **enhancement**（不是 bug）被拒绝为 `wontfix` 时才写入。流程：

1. Maintainer 决定一个 feature request 不在范围内
2. 检查是否已存在匹配的 `.out-of-scope/` file
3. 如果存在：把新 issue 追加到 "Prior requests" list
4. 如果不存在：用 concept name、decision、reason 和第一个 prior request 创建新 file
5. 在 issue 上发布 comment，解释 decision 并提到 `.out-of-scope/` file
6. 用 `wontfix` label close issue

## Updating or removing out-of-scope files

如果 maintainer 改变了对 previously rejected concept 的想法：

- 删除 `.out-of-scope/` file
- Skill 不需要 reopen old issues；它们是 historical records
- 触发 reconsideration 的新 issue 继续正常 triage
