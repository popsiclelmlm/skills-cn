# Interface Design for Testability

好的 interfaces 会让 testing 变得自然：

1. **接受 dependencies，不要创建它们**

   ```typescript
   // Testable
   function processOrder(order, paymentGateway) {}

   // Hard to test
   function processOrder(order) {
     const gateway = new StripeGateway();
   }
   ```

2. **返回 results，不要产生 side effects**

   ```typescript
   // Testable
   function calculateDiscount(cart): Discount {}

   // Hard to test
   function applyDiscount(cart): void {
     cart.total -= discount;
   }
   ```

3. **Small surface area**
   - Fewer methods = 需要的 tests 更少
   - Fewer params = test setup 更简单
