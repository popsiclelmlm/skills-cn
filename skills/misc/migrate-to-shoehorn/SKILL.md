---
name: migrate-to-shoehorn
description: 把 test files 中的 `as` type assertions 迁移到 @total-typescript/shoehorn。当用户提到 shoehorn、想替换 tests 中的 `as`，或需要 partial test data 时使用。
---

# Migrate to Shoehorn

## Why shoehorn?

`shoehorn` 允许你在 tests 中传入 partial data，同时让 TypeScript 保持满意。它用 type-safe alternatives 替代 `as` assertions。

**只用于 test code。** 绝不要在 production code 中使用 shoehorn。

Tests 中 `as` 的问题：

- 我们被训练成不要使用它
- 必须手动指定 target type
- 为了 intentionally wrong data 需要 double-as（`as unknown as Type`）

## Install

```bash
npm i @total-typescript/shoehorn
```

## Migration patterns

### 只需要少数属性的大对象

Before:

```ts
type Request = {
  body: { id: string };
  headers: Record<string, string>;
  cookies: Record<string, string>;
  // ...20 more properties
};

it("gets user by id", () => {
  // Only care about body.id but must fake entire Request
  getUser({
    body: { id: "123" },
    headers: {},
    cookies: {},
    // ...fake all 20 properties
  });
});
```

After:

```ts
import { fromPartial } from "@total-typescript/shoehorn";

it("gets user by id", () => {
  getUser(
    fromPartial({
      body: { id: "123" },
    }),
  );
});
```

### `as Type` → `fromPartial()`

Before:

```ts
getUser({ body: { id: "123" } } as Request);
```

After:

```ts
import { fromPartial } from "@total-typescript/shoehorn";

getUser(fromPartial({ body: { id: "123" } }));
```

### `as unknown as Type` → `fromAny()`

Before:

```ts
getUser({ body: { id: 123 } } as unknown as Request); // wrong type on purpose
```

After:

```ts
import { fromAny } from "@total-typescript/shoehorn";

getUser(fromAny({ body: { id: 123 } }));
```

## When to use each

| Function        | Use case                                           |
| --------------- | -------------------------------------------------- |
| `fromPartial()` | 传入仍能 type-check 的 partial data                |
| `fromAny()`     | 传入 intentionally wrong data（保留 autocomplete） |
| `fromExact()`   | 强制 full object（稍后可换成 fromPartial）         |

## Workflow

1. **Gather requirements** — 询问用户：
   - 哪些 test files 中有造成问题的 `as` assertions？
   - 他们是否在处理只有部分属性重要的大对象？
   - 是否需要为 error testing 传入 intentionally wrong data？

2. **Install and migrate**：
   - [ ] Install：`npm i @total-typescript/shoehorn`
   - [ ] 查找带 `as` assertions 的 test files：`grep -r " as [A-Z]" --include="*.test.ts" --include="*.spec.ts"`
   - [ ] 把 `as Type` 替换为 `fromPartial()`
   - [ ] 把 `as unknown as Type` 替换为 `fromAny()`
   - [ ] 添加来自 `@total-typescript/shoehorn` 的 imports
   - [ ] 运行 type check 验证
