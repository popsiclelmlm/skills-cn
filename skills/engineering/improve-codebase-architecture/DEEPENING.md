# Deepening

在给定 dependencies 的情况下，如何安全地加深一组 shallow modules。假设使用 [LANGUAGE.md](LANGUAGE.md) 中的 vocabulary：**module**、**interface**、**seam**、**adapter**。

## Dependency categories

评估 deepening candidate 时，对它的 dependencies 分类。类别决定 deepened module 如何跨 seam 测试。

### 1. In-process

Pure computation、in-memory state、无 I/O。总是可 deepenable：合并 modules，并直接通过新 interface 测试。不需要 adapter。

### 2. Local-substitutable

有 local test stand-ins 的 dependencies（Postgres 的 PGLite、in-memory filesystem）。如果 stand-in 存在，就可 deepenable。Deepened module 在 test suite 中用运行中的 stand-in 测试。Seam 是内部的；module 的 external interface 上没有 port。

### 3. Remote but owned（Ports & Adapters）

跨 network boundary 的你自己的 services（microservices、internal APIs）。在 seam 上定义一个 **port**（interface）。Deep module 拥有 logic；transport 作为 **adapter** 注入。Tests 使用 in-memory adapter。Production 使用 HTTP/gRPC/queue adapter。

Recommendation shape：*"Define a port at the seam, implement an HTTP adapter for production and an in-memory adapter for testing, so the logic sits in one deep module even though it's deployed across a network."*

### 4. True external（Mock）

你不控制的 third-party services（Stripe、Twilio 等）。Deepened module 把 external dependency 作为 injected port；tests 提供 mock adapter。

## Seam discipline

- **One adapter means a hypothetical seam. Two adapters means a real one.** 除非至少两个 adapters 有正当理由（通常是 production + test），否则不要引入 port。Single-adapter seam 只是 indirection。
- **Internal seams vs external seams.** Deep module 可以有 internal seams（对 implementation 私有，用于自己的 tests），也可以有位于 interface 上的 external seam。不要只因为 tests 使用 internal seams，就把它们通过 interface 暴露出去。

## Testing strategy: replace, don't layer

- 一旦 deepened module interface 上的 tests 存在，旧的 shallow modules unit tests 就成了 waste：删除它们。
- 在 deepened module 的 interface 上写 new tests。**Interface is the test surface**。
- Tests 通过 interface 对 observable outcomes 做 assertions，而不是 internal state。
- Tests 应经受 internal refactors：它们描述 behaviour，而不是 implementation。如果 implementation 变化时 test 必须变化，它就是越过了 interface 在测试。
