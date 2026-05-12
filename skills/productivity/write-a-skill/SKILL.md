---
name: write-a-skill
description: 创建结构合适、具备 progressive disclosure 和 bundled resources 的新 agent skills。当用户想创建、编写或构建一个新 skill 时使用。
---

# Writing Skills

## Process

1. **Gather requirements** — 询问用户：
   - 这个 skill 覆盖什么 task/domain？
   - 它应该处理哪些具体 use cases？
   - 它需要 executable scripts，还是只需要 instructions？
   - 是否有要包含的 reference materials？

2. **Draft the skill** — 创建：
   - 包含简洁 instructions 的 SKILL.md
   - 如果内容超过 500 行，创建额外 reference files
   - 如果需要 deterministic operations，创建 utility scripts

3. **Review with user** — 展示 draft 并询问：
   - 这是否覆盖了你的 use cases？
   - 有没有缺失或不清楚的内容？
   - 是否有任何 section 应该更详细或更简略？

## Skill Structure

```
skill-name/
├── SKILL.md           # Main instructions (required)
├── REFERENCE.md       # Detailed docs (if needed)
├── EXAMPLES.md        # Usage examples (if needed)
└── scripts/           # Utility scripts (if needed)
    └── helper.js
```

## SKILL.md Template

```md
---
name: skill-name
description: Brief description of capability. Use when [specific triggers].
---

# Skill Name

## Quick start

[Minimal working example]

## Workflows

[Step-by-step processes with checklists for complex tasks]

## Advanced features

[Link to separate files: See [REFERENCE.md](REFERENCE.md)]
```

## Description Requirements

description 是 agent 决定加载哪个 skill 时**唯一能看到的内容**。它会和所有其他已安装 skills 一起出现在 system prompt 中。你的 agent 会读取这些 descriptions，并根据用户请求选择相关 skill。

**Goal**：给 agent 足够的信息，让它知道：

1. 这个 skill 提供什么能力
2. 何时/为何触发它（specific keywords、contexts、file types）

**Format**：

- 最多 1024 chars
- 用 third person 编写
- 第一句：它做什么
- 第二句："Use when [specific triggers]"

**Good example**：

```
Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when user mentions PDFs, forms, or document extraction.
```

**Bad example**：

```
Helps with documents.
```

坏示例无法让 agent 把它和其他 document skills 区分开。

## When to Add Scripts

在以下情况添加 utility scripts：

- Operation 是 deterministic（validation、formatting）
- 同一段 code 会被反复生成
- Errors 需要显式处理

相比 generated code，Scripts 能节省 tokens 并提升可靠性。

## When to Split Files

在以下情况拆分为单独文件：

- SKILL.md 超过 100 行
- 内容包含不同 domains（finance vs sales schemas）
- Advanced features 很少需要

## Review Checklist

Draft 完成后验证：

- [ ] Description 包含 triggers（"Use when..."）
- [ ] SKILL.md 少于 100 行
- [ ] 没有 time-sensitive info
- [ ] 术语一致
- [ ] 包含具体 examples
- [ ] References 只深入一层
