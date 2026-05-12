# 只为硬依赖显式提示 `/setup-matt-pocock-skills`

Engineering skills 依赖 `/setup-matt-pocock-skills` 种下的每仓库配置（issue tracker、triage label vocabulary、domain doc layout）。有些 skills 没有这些配置就无法有意义地工作：它们必须发布到特定 issue tracker，或应用特定标签字符串。另一些 skills 只是用这些配置来让输出更精准（词汇、ADR 感知），没有它们也能优雅降级。

我们把它们拆分为 **hard-dependency** 和 **soft-dependency** skills：

- **Hard dependency**（`to-issues`、`to-prd`、`triage`）— 包含一句显式提示：_“… should have been provided to you — run `/setup-matt-pocock-skills` if not.”_ 没有映射时，输出会是错误的，而不仅仅是不够清晰。
- **Soft dependency**（`diagnose`、`tdd`、`improve-codebase-architecture`、`zoom-out`）— 只用较宽泛的文字引用 “the project's domain glossary” 和 “ADRs in the area you're touching”。如果这些 docs 不存在，skill 仍然可用；输出只是没那么锋利。

这种拆分让 soft-dependency skills 保持低 token 开销，也避免把 setup pointer 机械塞进并不承重的位置。
