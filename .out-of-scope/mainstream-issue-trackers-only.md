# Issue tracker 集成仅限主流工具

`setup-matt-pocock-skills` 只为**主流** issue trackers 提供一等支持。为小众、新兴或单一供应商的实验性 trackers 添加支持的请求不在范围内。

## 为什么这不在范围内

每个 issue-tracker backend 都会把一种 CLI 形状硬编码进 skills（commands、flags、output parsing）。每新增一个 backend，都会带来永久维护面：它必须随着该工具 CLI 的演进持续工作，也必须持续针对 `/to-prd`、`/to-issues`、`/triage` 等进行测试。只有当相当一部分用户确实拥有某个 tracker 时，这个成本才值得支付。

“Mainstream” 是判断题，不是数字门槛：

- GitHub、GitLab 和 Backlog.md 属于我们会认为 mainstream 的工具：广为人知、广泛使用，并且早已过了实验阶段。
- 一个只有几百 GitHub stars 的全新 agent-focused 工具不是 mainstream，无论它的设计多有意思。

stars、年龄和下载量在判断时都是有用信号，但没有一个是规则。规则是：一位典型工程师是否会认识这个工具，并且有可能已经为团队选择它？

非主流 trackers 已经有逃生口：

- `local markdown` 用于轻量的仓库内追踪。
- `other/custom` 用于想自己接线的用户。

两者都不要求核心 skills 理解具体工具。

## Prior requests

- #99 — “Add dex as an issue tracker backend”（提出请求时 dex 大约 3 个月大、约 300 stars）
