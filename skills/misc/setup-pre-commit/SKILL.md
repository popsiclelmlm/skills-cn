---
name: setup-pre-commit
description: 在当前 repo 中设置带 lint-staged（Prettier）、type checking 和 tests 的 Husky pre-commit hooks。当用户想添加 pre-commit hooks、设置 Husky、配置 lint-staged，或添加 commit-time formatting/typechecking/testing 时使用。
---

# Setup Pre-Commit Hooks

## What This Sets Up

- **Husky** pre-commit hook
- **lint-staged**，对所有 staged files 运行 Prettier
- **Prettier** config（如果缺失）
- pre-commit hook 中的 **typecheck** 和 **test** scripts

## Steps

### 1. Detect package manager

检查 `package-lock.json`（npm）、`pnpm-lock.yaml`（pnpm）、`yarn.lock`（yarn）、`bun.lockb`（bun）。使用存在的那个。如果不清楚，默认使用 npm。

### 2. Install dependencies

作为 devDependencies 安装：

```
husky lint-staged prettier
```

### 3. Initialize Husky

```bash
npx husky init
```

这会创建 `.husky/` dir，并向 package.json 添加 `prepare: "husky"`。

### 4. Create `.husky/pre-commit`

写入这个文件（Husky v9+ 不需要 shebang）：

```
npx lint-staged
npm run typecheck
npm run test
```

**Adapt**：把 `npm` 替换为检测到的 package manager。如果 repo 的 package.json 中没有 `typecheck` 或 `test` script，省略对应行并告诉用户。

### 5. Create `.lintstagedrc`

```json
{
  "*": "prettier --ignore-unknown --write"
}
```

### 6. Create `.prettierrc`（如果缺失）

只有在不存在 Prettier config 时才创建。使用这些 defaults：

```json
{
  "useTabs": false,
  "tabWidth": 2,
  "printWidth": 80,
  "singleQuote": false,
  "trailingComma": "es5",
  "semi": true,
  "arrowParens": "always"
}
```

### 7. Verify

- [ ] `.husky/pre-commit` 存在且可执行
- [ ] `.lintstagedrc` 存在
- [ ] package.json 中的 `prepare` script 是 `"husky"`
- [ ] `prettier` config 存在
- [ ] 运行 `npx lint-staged` 验证可用

### 8. Commit

Stage 所有 changed/created files，并用这个 message 提交：`Add pre-commit hooks (husky + lint-staged + prettier)`

这会跑过新的 pre-commit hooks，是确认一切可用的好 smoke test。

## Notes

- Husky v9+ 不需要在 hook files 中写 shebangs
- `prettier --ignore-unknown` 会跳过 Prettier 无法解析的 files（images 等）
- pre-commit 会先运行 lint-staged（快，只处理 staged-only），然后运行完整 typecheck 和 tests
