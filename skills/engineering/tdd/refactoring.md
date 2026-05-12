# Refactor Candidates

TDD cycle 后，寻找：

- **Duplication** → Extract function/class
- **Long methods** → 拆成 private helpers（tests 仍保持在 public interface 上）
- **Shallow modules** → 合并或加深
- **Feature envy** → 把 logic 移到 data 所在处
- **Primitive obsession** → 引入 value objects
- new code 暴露为 problematic 的 **Existing code**
