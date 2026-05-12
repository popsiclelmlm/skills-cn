---
name: diagnose
description: 面向疑难 bug 和性能回退的纪律化诊断循环。Reproduce → minimise → hypothesise → instrument → fix → regression-test。当用户说 "diagnose this" / "debug this"、报告 bug、说某个东西 broken/throwing/failing，或描述 performance regression 时使用。
---

# Diagnose

一套处理疑难 bug 的纪律。只有在明确说明理由时才跳过阶段。

探索 codebase 时，使用项目的 domain glossary 来建立相关 modules 的清晰心智模型，并检查你正在触碰区域的 ADRs。

## Phase 1 — Build a feedback loop

**这就是这个 skill 的核心。** 其他一切都是机械动作。如果你有一个快速、确定、agent 可运行的 bug pass/fail 信号，你就会找到原因：bisection、hypothesis-testing 和 instrumentation 都只是消耗这个信号。如果没有，无论盯着代码看多久都救不了你。

在这里投入不成比例的努力。**大胆。创造性。拒绝放弃。**

### 构建反馈循环的方法 — 大致按这个顺序尝试

1. 在能触达 bug 的 seam 上写一个 **Failing test**：unit、integration、e2e 都可以。
2. 针对运行中的 dev server 写 **Curl / HTTP script**。
3. 用 fixture input 做 **CLI invocation**，把 stdout 与 known-good snapshot 做 diff。
4. 写 **Headless browser script**（Playwright / Puppeteer）：驱动 UI，对 DOM/console/network 做 assertions。
5. **Replay a captured trace.** 把真实 network request / payload / event log 保存到磁盘；隔离地重放到 code path 中。
6. **Throwaway harness.** 启动系统的最小子集（一个 service、mocked deps），用单个 function call 触发 bug code path。
7. **Property / fuzz loop.** 如果 bug 是 “sometimes wrong output”，运行 1000 个 random inputs 并寻找 failure mode。
8. **Bisection harness.** 如果 bug 出现在两个 known states（commit、dataset、version）之间，自动化 “boot at state X, check, repeat”，这样可以用 `git bisect run`。
9. **Differential loop.** 用相同 input 跑 old-version vs new-version（或两个 configs），然后 diff outputs。
10. **HITL bash script.** 最后的手段。如果必须有人点击，用 `scripts/hitl-loop.template.sh` 驱动_人_，让循环仍然有结构。捕获的 output 会反馈给你。

构建出正确反馈循环，bug 就已经修了 90%。

### 迭代反馈循环本身

把 loop 当成产品。一旦你有了_某个_ loop，问：

- 我能让它更快吗？（Cache setup、跳过无关 init、收窄 test scope。）
- 我能让信号更锋利吗？（Assert 具体 symptom，而不是 “didn't crash”。）
- 我能让它更确定吗？（Pin time、seed RNG、isolate filesystem、freeze network。）

一个 30 秒且 flaky 的 loop 只比没有 loop 略好。一个 2 秒且确定的 loop 是 debugging superpower。

### Non-deterministic bugs

目标不是干净复现，而是**更高 reproduction rate**。把 trigger 循环 100×、parallelise、加 stress、缩窄 timing windows、注入 sleeps。50%-flake bug 可调试；1% 不行。持续提高复现率，直到它可调试。

### 当你真的无法构建 loop

停下来并明确说明。列出你尝试过的内容。向用户请求：(a) 能复现它的环境访问权限，(b) 捕获的 artifact（HAR file、log dump、core dump、带 timestamps 的 screen recording），或 (c) 添加临时 production instrumentation 的许可。不要在没有 loop 的情况下继续 hypothesise。

在你拥有一个自己相信的 loop 前，不要进入 Phase 2。

## Phase 2 — Reproduce

运行 loop。看着 bug 出现。

确认：

- [ ] loop 产出的 failure mode 是**用户**描述的那个，而不是附近另一个失败。Wrong bug = wrong fix。
- [ ] failure 能在多次运行中复现（或对 non-deterministic bugs 来说，复现率足够高，可以据此 debug）。
- [ ] 你已经捕获了确切 symptom（error message、wrong output、slow timing），这样后续阶段才能验证 fix 确实解决它。

复现 bug 之前不要继续。

## Phase 3 — Hypothesise

在测试任何假设前，先生成 **3–5 个排序后的 hypotheses**。单一 hypothesis 生成会锚定在第一个貌似合理的想法上。

每个 hypothesis 都必须是 **falsifiable**：说明它做出的 prediction。

> Format: "If <X> is the cause, then <changing Y> will make the bug disappear / <changing Z> will make it worse."

如果你无法说明 prediction，这个 hypothesis 就只是 vibe。丢弃它或打磨它。

**在测试前把排序列表展示给用户。** 用户常常有能立刻重排它们的领域知识（“我们刚部署了 #3 的变更”），或知道哪些 hypotheses 已经被排除。这是便宜 checkpoint，节省大量时间。不要因此阻塞：如果用户 AFK，就按你的排序继续。

## Phase 4 — Instrument

每个 probe 都必须映射到 Phase 3 的某个具体 prediction。**一次只改变一个变量。**

工具偏好：

1. 如果 env 支持，优先使用 **Debugger / REPL inspection**。一个 breakpoint 胜过十条 logs。
2. 在能区分 hypotheses 的 boundaries 上打 **Targeted logs**。
3. 绝不要 “log everything and grep”。

**给每条 debug log 打唯一前缀**，例如 `[DEBUG-a4f2]`。结束时 cleanup 会变成一次 grep。未打标签的 logs 会存活；打了标签的 logs 会死。

**Perf branch.** 对 performance regressions 来说，logs 通常是错的。改为建立 baseline measurement（timing harness、`performance.now()`、profiler、query plan），然后 bisect。先测量，再修复。

## Phase 5 — Fix + regression test

在 fix 前写 regression test，但只有当存在**正确 seam** 时才这样做。

正确 seam 是指：test 会按 call site 实际发生的方式，触发**真实 bug pattern**。如果唯一可用的 seam 太浅（bug 需要多个 callers 而 test 只有 single-caller，或 unit test 无法复制触发 bug 的 chain），那里的 regression test 会给出虚假信心。

**如果不存在正确 seam，这本身就是发现。** 记下来。Codebase architecture 正在阻止 bug 被锁定。把这件事标记给下一阶段。

如果存在正确 seam：

1. 把 minimised repro 转成该 seam 上的 failing test。
2. 看它失败。
3. 应用 fix。
4. 看它通过。
5. 用原始（未 minimised）场景重新运行 Phase 1 feedback loop。

## Phase 6 — Cleanup + post-mortem

声明完成前必做：

- [ ] 原始 repro 不再复现（重新运行 Phase 1 loop）
- [ ] Regression test 通过（或记录不存在 seam）
- [ ] 所有 `[DEBUG-...]` instrumentation 已移除（`grep` 前缀）
- [ ] Throwaway prototypes 已删除（或移动到清晰标记的 debug location）
- [ ] 在 commit / PR message 中说明最终正确的 hypothesis，让下一个 debugger 能学到东西

**然后问：什么本可以防止这个 bug？** 如果答案涉及 architectural change（没有好的 test seam、tangled callers、hidden coupling），带着具体信息 hand off 给 `/improve-codebase-architecture` skill。这个 recommendation 要在 fix 已经完成后再给，不要提前给，因为你现在比开始时掌握了更多信息。
