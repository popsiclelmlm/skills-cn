# Matt Pocock Skills

一组供 Claude Code 加载的 agent skills（斜杠命令和行为）。Skills 按 bucket 组织，并由 `/setup-matt-pocock-skills` 生成的每仓库配置来消费。

## Language

**Issue tracker**：
承载某个仓库 issues 的工具，例如 GitHub Issues、Linear、本地 `.scratch/` markdown 约定或类似系统。`to-issues`、`to-prd`、`triage`、`qa` 等 skills 会读取并写入它。
_Avoid_：backlog manager、backlog backend、issue host

**Issue**：
**Issue tracker** 中一个被追踪的工作单元，例如 bug、task、PRD，或由 `to-issues` 生成的 slice。
_Avoid_：ticket（只有在引用外部系统并且它们称其为 ticket 时才使用）

**Triage role**：
在 triage 期间应用到某个 **Issue** 上的规范状态机标签，例如 `needs-triage`、`ready-for-afk`。每个 role 都会通过 `docs/agents/triage-labels.md` 映射到 **Issue tracker** 中真实的标签字符串。

## Relationships

- 一个 **Issue tracker** 包含多个 **Issues**
- 一个 **Issue** 同一时间只携带一个 **Triage role**

## Flagged ambiguities

- “backlog” 过去同时表示承载 issues 的*工具*和其中的*工作集合*。已解决：该工具称为 **Issue tracker**；“backlog” 不再作为领域术语使用。
- “backlog backend” / “backlog manager” — 已解决：统一收敛为 **Issue tracker**。
