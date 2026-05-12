# `setup-matt-pocock-skills` 的 Verify/Check Mode

本项目不会为 `setup-matt-pocock-skills` 添加专用 verify/check mode（或单独的 verify skill）。

## 为什么这不在范围内

增加第二个 skill，或为 `docs/agents/*.md` artifacts 是否仍匹配 seed-template schema 添加一个 `--verify` flag，会重复现有 setup skill 已经能在对话中处理的工作。

预期工作流是：**运行 `/setup-matt-pocock-skills` 并告诉它验证你当前的设置。** 该 skill 由 prompt 驱动，所以维护者可以把它限定为一次 verification pass（“don't rewrite anything, just check my existing files against the current seed templates and report drift”），而无需单独的代码路径。添加 flag 或 sibling skill 会把一个已经能通过自然语言入口表达的功能拆出额外表面。

把配置管理保持在单一 skill 中，也能避免 seed templates 演进时两个 skills 相互漂移的维护成本。

## Prior requests

- #106 — Feature request: verify/check mode for setup-matt-pocock-skills
