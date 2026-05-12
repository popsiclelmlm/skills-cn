<p>
  <a href="https://www.aihero.dev/s/skills-newsletter">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skills-repo-dark_2x.png">
      <source media="(prefers-color-scheme: light)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png">
      <img alt="Skills" src="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png" width="369">
    </picture>
  </a>
</p>

# Skills For Real Engineers

[![skills.sh](https://skills.sh/b/mattpocock/skills)](https://skills.sh/mattpocock/skills)

> 中文镜像说明：本仓库基于原项目 [mattpocock/skills](https://github.com/mattpocock/skills) 翻译维护。中文维护者：popsiclelmlm。原项目 copyright 归 Matt Pocock 所有，按 MIT License 分发。

这些是我每天用于真正工程工作的 agent skills，而不是 vibe coding。

开发真实应用很难。GSD、BMAD 和 Spec-Kit 这类方法试图通过接管流程来帮忙。但在这么做的同时，它们也会拿走你的控制权，并让流程里的 bug 变得难以解决。

这些 skills 被设计得小、易于调整、可组合。它们适用于任何模型，并建立在几十年的工程经验之上。随意改造它们，把它们变成你自己的。祝你用得开心。

如果你想跟上这些 skills 的变化，以及我创建的新 skills，可以加入我的 newsletter，那里还有大约 60,000 位开发者：

[Sign Up To The Newsletter](https://www.aihero.dev/s/skills-newsletter)

## Quickstart（30 秒设置）

1. 运行 skills.sh installer：

```bash
npx skills@latest add mattpocock/skills
```

2. 选择你想要的 skills，以及要把它们安装到哪些 coding agents 上。**务必选择 `/setup-matt-pocock-skills`**。

3. 在你的 agent 中运行 `/setup-matt-pocock-skills`。它会：
   - 询问你想使用哪个 issue tracker（GitHub、Linear 或 local files）
   - 询问你在 triage issues 时使用哪些 labels（`/triage` 会使用这些 labels）
   - 询问你想把我们创建的 docs 保存在哪里

4. 好了，可以开始。

## Why These Skills Exist

我构建这些 skills，是为了修复我在 Claude Code、Codex 和其他 coding agents 中看到的常见失败模式。

### #1：Agent 没有做我想要的事

> “没有人确切知道自己想要什么。”
>
> David Thomas & Andrew Hunt, [The Pragmatic Programmer](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**The Problem**。软件开发中最常见的失败模式是错位。你以为开发者知道你想要什么；然后你看到他们构建的东西，才意识到对方完全没理解你。

AI 时代也是一样。你和 agent 之间存在沟通鸿沟。修复方式是一场 **grilling session**：让 agent 围绕你要构建的东西提出细致问题。

**The Fix** 是使用：

- [`/grill-me`](./skills/productivity/grill-me/SKILL.md) — 用于非代码场景
- [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md) — 和 [`/grill-me`](./skills/productivity/grill-me/SKILL.md) 类似，但会加入更多好东西（见下文）

这些是我最受欢迎的 skills。它们帮助你在开始之前与 agent 对齐，并深入思考正在做的变更。每次想做变更时都应该使用它们。

### #2：Agent 太啰嗦

> 有了 ubiquitous language，开发者之间的对话和代码表达都会从同一个领域模型中派生出来。
>
> Eric Evans, [Domain-Driven-Design](https://www.amazon.co.uk/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215)

**The Problem**：在项目早期，开发者和他们为之构建软件的人（领域专家）通常说着不同语言。

我在 agent 身上也感到同样的张力。Agents 通常被丢进项目里，然后被要求边走边弄懂行话。于是它们用 20 个词表达 1 个词就能说清的事。

**The Fix** 是共享语言。它是一份帮助 agents 解码项目行话的文档。

<details>
<summary>
Example
</summary>

这里有一个来自我的 `course-video-manager` repo 的 [`CONTEXT.md`](https://github.com/mattpocock/course-video-manager/blob/076a5a7a182db0fe1e62971dd7a68bcadf010f1c/CONTEXT.md) 示例。哪一个更容易读？

- **BEFORE**：“当一个 course 的 section 中的 lesson 被变成 ‘real’（也就是在文件系统中获得位置）时会出问题。”
- **AFTER**：“materialization cascade 有问题。”

这种简洁会在一次又一次 session 中持续回报。

</details>

这已经内置在 [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md) 中。它是一场 grilling session，但还会帮助你与 AI 建立共享语言，并把难以解释的决策记录到 ADRs 中。

这有多强很难解释。它可能是这个 repo 中最酷的技术。试试看。

> [!TIP]
> 共享语言除了减少啰嗦之外还有很多好处：
>
> - **Variables、functions 和 files 会使用共享语言一致命名**
> - 因此，agent **更容易导航 codebase**
> - agent 也会因为能使用更简洁的语言而**花更少 tokens 思考**

### #3：代码无法工作

> “始终采取小而审慎的步骤。反馈速率就是你的速度上限。永远不要承担太大的任务。”
>
> David Thomas & Andrew Hunt, [The Pragmatic Programmer](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**The Problem**：假设你和 agent 已经对齐了要构建什么。如果 agent _仍然_ 产出糟糕代码，会发生什么？

这时要看你的反馈循环。没有关于代码实际运行情况的反馈，agent 就是在盲飞。

**The Fix**：你需要一组常规反馈循环：static types、browser access 和 automated tests。

对于 automated tests，red-green-refactor 循环至关重要。也就是 agent 先写一个失败测试，然后修复它。这能给 agent 一个稳定的反馈水平，从而产出好得多的代码。

我构建了一个 **[`/tdd`](./skills/engineering/tdd/SKILL.md) skill**，你可以把它塞进任何项目。它鼓励 red-green-refactor，并给 agent 提供大量关于好测试和坏测试的指导。

用于 debugging 时，我还构建了一个 **[`/diagnose`](./skills/engineering/diagnose/SKILL.md)** skill，把最佳 debugging 实践包装成一个简单循环。

### #4：我们构建出了一团烂泥

> “每天都要投入系统设计。”
>
> Kent Beck, [Extreme Programming Explained](https://www.amazon.co.uk/Extreme-Programming-Explained-Embrace-Change/dp/0321278658)

> “最好的 modules 是深的。它们通过简单接口提供大量功能。”
>
> John Ousterhout, [A Philosophy Of Software Design](https://www.amazon.co.uk/Philosophy-Software-Design-2nd/dp/173210221X)

**The Problem**：大多数由 agents 构建的 apps 都复杂且难以修改。因为 agents 能极大加速编码，它们也会加速软件熵增。Codebases 会以前所未有的速度变复杂。

**The Fix** 是一种全新的 AI-powered development 方法：关心代码设计。

这贯穿在这些 skills 的每一层中：

- [`/to-prd`](./skills/engineering/to-prd/SKILL.md) 在创建 PRD 前会询问你正在触碰哪些 modules
- [`/zoom-out`](./skills/engineering/zoom-out/SKILL.md) 会让 agent 在整个系统上下文中解释代码

更关键的是，[`/improve-codebase-architecture`](./skills/engineering/improve-codebase-architecture/SKILL.md) 会帮助你拯救已经变成一团烂泥的 codebase。我建议每隔几天就在你的 codebase 上运行一次。

### Summary

软件工程基础比以往任何时候都更重要。这些 skills 是我把这些基础压缩成可重复实践的最佳努力，希望能帮助你交付职业生涯中最好的 apps。祝你用得开心。

## Reference

### Engineering

我日常用于代码工作的 skills。

- **[diagnose](./skills/engineering/diagnose/SKILL.md)** — 面向疑难 bug 和性能回退的纪律化诊断循环：reproduce → minimise → hypothesise → instrument → fix → regression-test。
- **[grill-with-docs](./skills/engineering/grill-with-docs/SKILL.md)** — 一场 grilling session，会用既有领域模型挑战你的计划、打磨术语，并内联更新 `CONTEXT.md` 和 ADRs。
- **[triage](./skills/engineering/triage/SKILL.md)** — 通过 triage roles 的状态机来 triage issues。
- **[improve-codebase-architecture](./skills/engineering/improve-codebase-architecture/SKILL.md)** — 根据 `CONTEXT.md` 中的领域语言和 `docs/adr/` 中的决策，寻找让 codebase 更深入的机会。
- **[setup-matt-pocock-skills](./skills/engineering/setup-matt-pocock-skills/SKILL.md)** — 搭建其他 engineering skills 会消费的每仓库配置（issue tracker、triage label vocabulary、domain doc layout）。每个 repo 使用 `to-issues`、`to-prd`、`triage`、`diagnose`、`tdd`、`improve-codebase-architecture` 或 `zoom-out` 前运行一次。
- **[tdd](./skills/engineering/tdd/SKILL.md)** — 使用 red-green-refactor 循环进行测试驱动开发。一次构建或修复一个 vertical slice。
- **[to-issues](./skills/engineering/to-issues/SKILL.md)** — 使用 vertical slices，把任意 plan、spec 或 PRD 拆成可独立领取的 GitHub issues。
- **[to-prd](./skills/engineering/to-prd/SKILL.md)** — 把当前对话上下文转成 PRD，并作为 GitHub issue 提交。不做访谈，只综合你已经讨论过的内容。
- **[zoom-out](./skills/engineering/zoom-out/SKILL.md)** — 让 agent zoom out，从整个系统的上下文解释一段不熟悉的代码，或给出更高层视角。
- **[prototype](./skills/engineering/prototype/SKILL.md)** — 构建一次性 prototype 来充实设计：可以是面向 state/business-logic 问题的可运行 terminal app，也可以是在同一路由中可切换的多种激进 UI 变体。

### Productivity

通用工作流工具，不专属于代码。

- **[caveman](./skills/productivity/caveman/SKILL.md)** — 超压缩沟通模式。通过去掉填充内容将 token 用量减少约 75%，同时保持完整技术准确性。
- **[grill-me](./skills/productivity/grill-me/SKILL.md)** — 围绕计划或设计进行毫不松手的访谈，直到决策树的每个分支都被解决。
- **[handoff](./skills/productivity/handoff/SKILL.md)** — 把当前对话压缩成 handoff document，让另一个 agent 可以继续工作。
- **[write-a-skill](./skills/productivity/write-a-skill/SKILL.md)** — 用合适结构、progressive disclosure 和 bundled resources 创建新的 skills。

### Misc

我保留下来但很少使用的工具。

- **[git-guardrails-claude-code](./skills/misc/git-guardrails-claude-code/SKILL.md)** — 设置 Claude Code hooks，在危险 git commands（push、reset --hard、clean 等）执行前阻止它们。
- **[migrate-to-shoehorn](./skills/misc/migrate-to-shoehorn/SKILL.md)** — 把 test files 中的 `as` type assertions 迁移到 @total-typescript/shoehorn。
- **[scaffold-exercises](./skills/misc/scaffold-exercises/SKILL.md)** — 创建包含 sections、problems、solutions 和 explainers 的 exercise directory structures。
- **[setup-pre-commit](./skills/misc/setup-pre-commit/SKILL.md)** — 设置带 lint-staged、Prettier、type checking 和 tests 的 Husky pre-commit hooks。
