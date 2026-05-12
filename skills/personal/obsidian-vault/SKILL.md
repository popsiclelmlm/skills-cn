---
name: obsidian-vault
description: 在 Obsidian vault 中使用 wikilinks 和 index notes 搜索、创建并管理 notes。当用户想在 Obsidian 中查找、创建或组织 notes 时使用。
---

# Obsidian Vault

## Vault location

`/mnt/d/Obsidian Vault/AI Research/`

根层级基本是扁平结构。

## Naming conventions

- **Index notes**：聚合相关主题（例如 `Ralph Wiggum Index.md`、`Skills Index.md`、`RAG Index.md`）
- 所有 note names 使用 **Title case**
- 不用 folders 做组织。改用 links 和 index notes

## Linking

- 使用 Obsidian `[[wikilinks]]` syntax：`[[Note Title]]`
- Notes 在底部链接到 dependencies/related notes
- Index notes 只是 `[[wikilinks]]` 列表

## Workflows

### Search for notes

```bash
# Search by filename
find "/mnt/d/Obsidian Vault/AI Research/" -name "*.md" | grep -i "keyword"

# Search by content
grep -rl "keyword" "/mnt/d/Obsidian Vault/AI Research/" --include="*.md"
```

或者直接在 vault path 上使用 Grep/Glob tools。

### Create a new note

1. 文件名使用 **Title Case**
2. 把内容写成一个 learning unit（按 vault rules）
3. 在底部添加指向 related notes 的 `[[wikilinks]]`
4. 如果属于 numbered sequence，使用 hierarchical numbering scheme

### Find related notes

在整个 vault 中搜索 `[[Note Title]]` 来查找 backlinks：

```bash
grep -rl "\\[\\[Note Title\\]\\]" "/mnt/d/Obsidian Vault/AI Research/"
```

### Find index notes

```bash
find "/mnt/d/Obsidian Vault/AI Research/" -name "*Index*"
```
