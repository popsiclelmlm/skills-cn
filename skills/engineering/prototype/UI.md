# UI Prototype

在单一路由上生成**多个激进不同的 UI variations**，并通过 floating bottom bar 切换。用户在 browser 中翻看 variants，选择一个（或从每个里面偷一些部分），然后把其余丢掉。

如果问题是 logic/state，而不是某个东西看起来应该怎样，那是错误分支。使用 [LOGIC.md](LOGIC.md)。

## When this is the right shape

- "What should this page look like?"
- "I want to see a few options for this dashboard before committing."
- "Try a different layout for the settings screen."
- 任何用户本来会花一天时间在脑中三种模糊 mockups 之间摇摆的场景。

## Two sub-shapes — strongly prefer sub-shape A

当 UI prototype **贴着 app 其他部分**时，它更容易判断：real header、real sidebar、real data、real density。孤立的 throwaway route 是真空：每个 variant 单独看都不错。只要有合理的现有 page 可以承载 variants，就默认使用 sub-shape A。只有 prototype 确实没有 nearby home 时，才使用 sub-shape B。

### Sub-shape A — adjustment to an existing page（preferred）

Route 已经存在。Variants 渲染在**同一路由**，由 `?variant=` URL search param 控制。现有 data fetching、params 和 auth 都保留，只有 rendering 切换。这是默认选择；除非有具体理由，否则选择它。

如果 prototype 的东西还没有 page，但_自然会住在某个现有 page 里_（dashboard 的新 section、settings screen 上的新 card、existing flow 中的新 step），仍然是 sub-shape A。把 variants mount 到 host page 里。

### Sub-shape B — a new page（last resort）

只有当被 prototype 的东西确实没有现有 page 可住时才使用它：例如完全新的 top-level surface，或无法合理嵌入任何地方的 flow。

按项目已有 routing convention 创建一个 **throwaway route**，不要发明新的 top-level structure。命名要明显是 prototype（例如 path 或 filename 中包含 `prototype`）。同样使用 `?variant=` pattern。

在承诺使用 sub-shape B 前，sanity-check：真的没有现有 page 可以嵌入吗？空 route 会隐藏有数据页面会暴露的 design problems。

两种 sub-shapes 中，floating bottom bar 完全相同。

## Process

### 1. State the question and pick N

默认使用 **3 variants**。超过 5 个就不再是激进不同，而开始变成噪音：上限 5。

在 prototype 所在位置或文件顶部 comment 中，用一行写下 plan：

> "Three variants of the settings page, switchable via `?variant=`, on the existing `/settings` route."

这在用户在线能 push back 时有效，用户不在线时也有效。

### 2. Generate radically different variants

起草每个 variant。每个都必须符合：

- Page 的 purpose 以及它能访问的数据。
- 项目的 component library / styling system（TailwindCSS、shadcn、MUI、plain CSS 等）。
- 清晰的 exported component name，例如 `VariantA`、`VariantB`、`VariantC`。

Variants 必须**结构性不同**：不同 layout、不同 information hierarchy、不同 primary affordance，而不只是颜色不同。三个稍微改过的 card grids 不是 UI prototype，而是 wallpaper。如果两个 drafts 太相似，带着明确的 “do not use a card grid” guidance 重做其中一个。

### 3. Wire them together

在 route 上创建单个 switcher component：

```tsx
// pseudo-code — adapt to the project's framework
const variant = searchParams.get('variant') ?? 'A';
return (
  <>
    {variant === 'A' && <VariantA {...data} />}
    {variant === 'B' && <VariantB {...data} />}
    {variant === 'C' && <VariantC {...data} />}
    <PrototypeSwitcher variants={['A','B','C']} current={variant} />
  </>
);
```

对 sub-shape A（existing page）：保留 switcher 上方所有 existing data fetching；只有 rendered subtree 随 variant 改变。

对 sub-shape B（new page）：`/prototype/<name>` 下的 throwaway route mount 同一个 switcher。

### 4. Build the floating switcher

屏幕底部居中的小型 fixed-position bar，包含三部分：

- **Left arrow** — 循环到 previous variant（wraps around）。
- **Variant label** — 显示当前 variant key；如果 variant 导出 name，也显示 name。例如 `B — Sidebar layout`。
- **Right arrow** — 循环前进（wraps around）。

Behaviour:

- 点击 arrow 会更新 URL search param（使用 framework router：Next 用 `router.replace`，React Router 用 `navigate` 等），让 variant 可分享并在 reload 后稳定。
- Keyboard：`←` 和 `→` arrow keys 也可循环。焦点在 `<input>`、`<textarea>` 或 `[contenteditable]` 时不要拦截 arrow keys。
- 视觉上与 page 区分开（例如 high-contrast pill、subtle shadow），让人明显看出它不是正在评估的 design 的一部分。
- Production builds 中隐藏：用 `process.env.NODE_ENV !== 'production'` 或等价检查 gate 住，避免 prototype merge 后把 bar 发给 users。

把 switcher 放在单个 shared component 中，让两种 sub-shapes 都能复用。放到项目 shared UI 所在位置。

### 5. Hand it over

展示 URL（以及 `?variant=` keys）。用户有空时会自己翻看。有趣反馈通常是 **"I want the header from B with the sidebar from C"**，这才是他们真正想要的 design。

### 6. Capture the answer and clean up

一旦某个 variant 胜出，写下哪个胜出以及原因（commit message、ADR、issue，或如果 AFK 运行且用户尚未回应，则在 prototype 旁边写 `NOTES.md`）。然后：

- **Sub-shape A** — 删除失败 variants 和 switcher；把 winner 折入 existing page。
- **Sub-shape B** — 把 winning variant 提升为 real route，删除 throwaway route 和 switcher。

不要留下 variant components 或 switcher。它们很快腐烂，并让下一位读者困惑。

## Anti-patterns

- **Variants 只在 colour 或 copy 上不同。** 那是 tweak，不是 prototype。真正 variants 会在 structure 上意见相左。
- **Variants 之间共享太多 code。** 共享 `<Header>` 可以；共享 `<Layout>` 会击败目的。每个 variant 都应该有自由丢掉 layout。
- **把 variants 接到真实 mutations。** Read-only prototypes 可以。如果 variant 需要 mutate，把它指向 stub：问题是 “what should this look like”，不是 “does the backend work”。
- **直接把 prototype 提升为 production。** Variant code 是在 prototype constraints 下写的（没有 tests、minimal error handling）。折入时要正确重写。
