---
name: to-issues
description: 使用 tracer-bullet vertical slices，把 plan、spec 或 PRD 拆成可在项目 issue tracker 上独立领取的 issues。当用户想把 plan 转成 issues、创建 implementation tickets，或把工作拆成 issues 时使用。
---

# To Issues

使用 vertical slices（tracer bullets）把 plan 拆成可独立领取的 issues。

Issue tracker 和 triage label vocabulary 应该已经提供给你；如果没有，运行 `/setup-matt-pocock-skills`。

## Process

### 1. Gather context

从对话上下文中已经存在的任何内容开始。如果用户把 issue reference（issue number、URL 或 path）作为 argument 传入，就从 issue tracker 拉取它，并读取完整 body 和 comments。

### 2. Explore the codebase（optional）

如果你还没有探索 codebase，就先做，以理解当前 code 状态。Issue titles 和 descriptions 应使用项目的 domain glossary vocabulary，并尊重你正在触碰区域的 ADRs。

### 3. Draft vertical slices

把 plan 拆成 **tracer bullet** issues。每个 issue 都是一个薄 vertical slice，会 end-to-end 穿过所有 integration layers，而不是某一层的 horizontal slice。

Slices 可以是 `HITL` 或 `AFK`。HITL slices 需要 human interaction，例如 architectural decision 或 design review。AFK slices 可以在没有 human interaction 的情况下实现并 merge。尽可能偏好 AFK 而不是 HITL。

<vertical-slice-rules>
- 每个 slice 都交付一条穿过每一层的窄但 COMPLETE 的路径（schema、API、UI、tests）
- 完成的 slice 本身可 demo 或 verify
- 偏好很多薄 slices，而不是少数厚 slices
</vertical-slice-rules>

### 4. Quiz the user

以编号列表展示 proposed breakdown。对每个 slice，展示：

- **Title**：简短描述性名称
- **Type**：HITL / AFK
- **Blocked by**：哪些其他 slices（如有）必须先完成
- **User stories covered**：它覆盖哪些 user stories（如果 source material 有）

询问用户：

- Granularity 感觉对吗？（too coarse / too fine）
- Dependency relationships 正确吗？
- 是否应该 merge 或 further split 任何 slices？
- 哪些 slices 标为 HITL 和 AFK 是否正确？

迭代直到用户批准 breakdown。

### 5. Publish the issues to the issue tracker

对每个 approved slice，在 issue tracker 上发布一个新 issue。使用下面的 issue body template。这些 issues 被视为 ready for AFK agents，所以除非另有指示，发布时带上正确 triage label。

按 dependency order 发布 issues（blockers first），这样你可以在 "Blocked by" field 中引用真实 issue identifiers。

<issue-template>
## Parent

A reference to the parent issue on the issue tracker (if the source was an existing issue, otherwise omit this section).

## What to build

A concise description of this vertical slice. Describe the end-to-end behavior, not layer-by-layer implementation.

Avoid specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it here and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Blocked by

- A reference to the blocking ticket (if any)

Or "None - can start immediately" if no blockers.

</issue-template>

不要 close 或 modify 任何 parent issue。
