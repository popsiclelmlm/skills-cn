---
name: prototype
description: 在承诺实现前构建 throwaway prototype 来充实设计。会在两个分支之间路由：用于 state/business-logic 问题的可运行 terminal app，或在单一路由中可切换的多个激进不同 UI variations。当用户想 prototype、sanity-check data model 或 state machine、mock up UI、探索 design options，或说 "prototype this"、"let me play with it"、"try a few designs" 时使用。
---

# Prototype

Prototype 是**回答一个问题的一次性代码**。问题决定形状。

## Pick a branch

识别正在回答的问题：来自用户 prompt、周围 code，或在用户在线时询问：

- **"Does this logic / state model feel right?"** → [LOGIC.md](LOGIC.md)。构建一个小型 interactive terminal app，把 state machine 推过那些纸面上难以推理的 cases。
- **"What should this look like?"** → [UI.md](UI.md)。在单一路由上生成多个激进不同的 UI variations，并通过 URL search param 和 floating bottom bar 切换。

两个分支会产出非常不同的 artifacts；选错会浪费整个 prototype。如果问题确实模糊且用户不可达，默认选择更匹配周围 code 的分支（backend module → logic；page 或 component → UI），并在 prototype 顶部说明 assumption。

## Rules that apply to both

1. **从第一天起就是 throwaway，并清晰标记。** 把 prototype code 放在它实际会被使用的位置附近（靠近它要 prototype 的 module 或 page），让 context 明显；但命名要让 casual reader 看出它是 prototype，不是 production。对 throwaway UI routes，遵守项目已有 routing convention，不要发明新的 top-level structure。
2. **一个命令即可运行。** 使用项目现有 task runner 支持的形式：`pnpm <name>`、`python <path>`、`bun <path>` 等。用户必须不用思考就能启动它。
3. **默认无 persistence。** State 存在 memory 中。Persistence 是 prototype 要_检查_的东西，不是它应该依赖的东西。如果问题明确涉及 database，使用 scratch DB 或带清晰 "PROTOTYPE — wipe me" 名称的 local file。
4. **跳过 polish。** 不写 tests，不做超出让 prototype _runnable_ 所需的 error handling，不做 abstractions。重点是快速学到东西，然后删除。
5. **展示 state。** 每个 action（logic）后或每次 variant switch（UI）时，print 或 render 完整相关 state，让用户看到变化。
6. **完成后删除或吸收。** 当 prototype 已回答问题，要么删除它，要么把验证过的 decision 折入真实 code。不要让它在 repo 中腐烂。

## When done

Prototype 中唯一值得保留的是_答案_。把答案和它回答的问题一起捕获到 durable 位置（commit message、ADR、issue，或 prototype 旁边的 `NOTES.md`）。如果用户在线，这个捕获是一次快速对话；如果不在线，留下 placeholder，让他们（或下一次的你）在删除 prototype 前填入 verdict。
