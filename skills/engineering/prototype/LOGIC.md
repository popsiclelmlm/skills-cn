# Logic Prototype

一个小型 interactive terminal app，让用户手动驱动 state model。当问题关于 **business logic、state transitions 或 data shape** 时使用：这些东西在纸面上看起来合理，但只有推过真实 cases 后才会感觉哪里不对。

## When this is the right shape

- "I'm not sure if this state machine handles the edge case where X then Y."
- "Does this data model actually let me represent the case where..."
- "I want to feel out what the API should look like before writing it."
- 任何用户想**按按钮并观察 state 变化**的场景。

如果问题是 "what should this look like"，那是错误分支。使用 [UI.md](UI.md)。

## Process

### 1. State the question

写 code 前，写下你正在 prototype 的 state model 和问题。用一段话，放在 prototype 的 README 中或文件顶部 comment 中。回答错误问题的 logic prototype 是纯浪费。把问题显式化，方便之后检查，无论用户现在是否在看，还是 AFK 后再回来。

### 2. Pick the language

使用 host project 使用的任何语言。如果 project 没有明显 runtime（例如 docs repo），询问用户。

匹配项目现有 tooling conventions。不要只为了 prototype 添加新的 package manager 或 runtime。

### 3. Isolate the logic in a portable module

把实际 logic，也就是回答问题的那一部分，放到一个 small、pure interface 后面，使其之后可以被提起并放入真实 codebase。围绕它的 TUI 是 throwaway；logic module 不应该是。

正确形状取决于问题：

- **A pure reducer** — `(state, action) => state`。适合 actions 是 discrete events 且 state 是单个值的情况。
- **A state machine** — 显式 states 和 transitions。适合 “which actions are even legal right now” 本身就是问题的一部分时。
- **A small set of pure functions** over a plain data type。适合没有 implicit current state，只有 transformations 的情况。
- **A class or module with a clear method surface**，当 logic 确实拥有持续 internal state 时。

选择最适合当前问题的形状，_不是_最容易接 TUI 的形状。保持 pure：没有 I/O、没有 terminal code、没有用 `console.log` 做 control flow。TUI import 它并调用它；不要让东西反向流动。

这让 prototype 在自身生命周期之后仍有用。当问题被回答后，验证过的 reducer / machine / function set 可以被提到真实 module 中；TUI shell 则被删除。

### 4. Build the smallest TUI that exposes the state

把它构建为 **lightweight TUI**：每个 tick 清屏（`console.clear()` / `print("\033[2J\033[H")` / equivalent），并重新 render 整个 frame。用户应该始终看到一个稳定 view，而不是不断增长的 scrollback。

每个 frame 有两部分，按此顺序：

1. **Current state**，pretty-printed 且 diff-friendly（每行一个 field，或 formatted JSON）。Field names 或 section headers 用 **bold**，不太重要的 context（timestamps、IDs、derived values）用 **dim**。原生 ANSI escape codes 就可以：`\x1b[1m` bold、`\x1b[2m` dim、`\x1b[0m` reset。除非项目已有 styling library，否则不需要引入。
2. **Keyboard shortcuts**，列在底部：`[a] add user  [d] delete user  [t] tick clock  [q] quit`。可以加粗 key、淡化 description，或反过来，按可读性选择。

Behaviour:

1. **Initialise state** — 单个 in-memory object/struct。启动时 render 第一帧。
2. 一次读取一个 keystroke（或一行），dispatch 给 handler，由 handler mutate state。
3. 每个 action 后重新 render 完整 frame：不要 append，要 replace。
4. Loop until quit。

整个 frame 应该适配一个屏幕。

### 5. Make it runnable in one command

向项目现有 task runner 添加 script（`package.json` scripts、`Makefile`、`justfile`、`pyproject.toml`）。用户应该运行 `pnpm run <prototype-name>` 或等价命令，而不需要记路径。

如果 host project 没有 task runner，就把 command 放在 prototype README 顶部。

### 6. Hand it over

给用户 run command。他们会自己驱动它；有意思的时刻是他们说 “wait, that shouldn't be possible” 或 “huh, I assumed X would be different”。这些就是_想法_里的 bugs，而这正是重点。如果他们想添加 new actions，就添加。Prototypes 会演化。

### 7. Capture the answer

当 prototype 完成任务时，对问题的答案是唯一值得保留的东西。如果用户在线，询问它教会了他们什么。如果不在线，在 prototype 旁边留下 `NOTES.md`，让答案可以被填入（或如果你看过 session，由你填入），然后再删除 prototype。

## Anti-patterns

- **不要添加 tests。** 需要 tests 的 prototype 不再是 prototype。
- **不要接入真实 database。** 除非问题专门关于 persistence，否则使用 in-memory store。
- **不要 generalise。** 不要 “what if we wanted to support X later”。Prototype 回答一个问题。
- **不要把 logic 和 TUI 混在一起。** 如果 reducer / state machine 引用了 `console.log`、prompts 或 terminal escape codes，它就不再 portable。让 TUI 成为 pure module 上的一层 thin shell。
- **不要把 TUI shell 发进 production。** Shell 针对人从 terminal 手动驱动而优化。它后面的 logic module 才是值得保留的部分。
