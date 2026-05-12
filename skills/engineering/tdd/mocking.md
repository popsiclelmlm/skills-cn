# When to Mock

只在 **system boundaries** 上 mock：

- External APIs（payment、email 等）
- Databases（有时；优先使用 test DB）
- Time/randomness
- File system（有时）

不要 mock：

- 你自己的 classes/modules
- Internal collaborators
- 任何你能控制的东西

## Designing for Mockability

在 system boundaries 上，设计容易 mock 的 interfaces：

**1. 使用 dependency injection**

传入 external dependencies，而不是在内部创建它们：

```typescript
// Easy to mock
function processPayment(order, paymentClient) {
  return paymentClient.charge(order.total);
}

// Hard to mock
function processPayment(order) {
  const client = new StripeClient(process.env.STRIPE_KEY);
  return client.charge(order.total);
}
```

**2. 优先使用 SDK-style interfaces，而不是 generic fetchers**

为每个 external operation 创建 specific functions，而不是一个带 conditional logic 的 generic function：

```typescript
// GOOD: Each function is independently mockable
const api = {
  getUser: (id) => fetch(`/users/${id}`),
  getOrders: (userId) => fetch(`/users/${userId}/orders`),
  createOrder: (data) => fetch('/orders', { method: 'POST', body: data }),
};

// BAD: Mocking requires conditional logic inside the mock
const api = {
  fetch: (endpoint, options) => fetch(endpoint, options),
};
```

SDK approach 意味着：
- 每个 mock 返回一个 specific shape
- Test setup 中没有 conditional logic
- 更容易看出 test exercise 了哪些 endpoints
- 每个 endpoint 有 type safety
