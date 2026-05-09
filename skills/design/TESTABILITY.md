# Designing for Testability

Three rules that make code testable without sacrificing clarity.

> Examples below use TypeScript for readability. The rules apply identically in Python (constructor injection + `Protocol`), Go (interface as parameter, fake struct in tests), Rust (trait object, mock impl), Kotlin / Java (interface + DI), and any other language with first-class function or interface values. Translate the syntax; the shape stays.

## 1. Accept dependencies, don't create them

```ts
// HARD TO TEST — gateway is hardcoded
function processOrder(order: Order) {
  const gateway = new StripeGateway(process.env.STRIPE_KEY);
  return gateway.charge(order.total);
}

// EASY TO TEST — gateway is passed in
function processOrder(order: Order, gateway: PaymentGateway) {
  return gateway.charge(order.total);
}
```

In tests, pass a fake or stub gateway. In production, pass the real one. No mocking framework needed.

## 2. Return results, don't produce side effects

```ts
// HARD TO TEST — mutates the cart, returns nothing
function applyDiscount(cart: Cart): void {
  cart.total -= computeDiscount(cart);
}

// EASY TO TEST — pure function
function calculateDiscount(cart: Cart): Discount {
  return { amount: computeDiscount(cart) };
}
```

Pure functions are the easiest things in software to test — same input, same output, no setup, no teardown.

## 3. Keep the surface area small

- Fewer methods → fewer tests needed.
- Fewer parameters → simpler test setup.
- Each method should do one thing.

## Mocking guidance

Mock only at **system boundaries**:

- External APIs (payment, email, third-party services)
- Time and randomness
- File system (sometimes — prefer a temp dir)
- Databases (sometimes — prefer a real test DB)

**Don't mock your own code.** If you find yourself mocking an internal class to test another internal class, the design is wrong — the two are too tightly coupled. Fix the interface so they can be tested independently, or test them together as one unit.

## SDK-style over generic fetchers

When wrapping an external API, prefer named methods over one generic call:

```ts
// GOOD — each call is independently mockable, type-safe per endpoint
const api = {
  getUser: (id: string) => fetch(`/users/${id}`),
  createOrder: (data: OrderInput) => fetch("/orders", { method: "POST", body: data }),
};

// BAD — mocking requires conditional logic in the mock
const api = {
  call: (endpoint: string, options?: object) => fetch(endpoint, options),
};
```

The SDK style means each test sets up exactly the calls it needs, and you can see at a glance which endpoints a test exercises.

## The test-difficulty signal

If a test is hard to write, the design is probably wrong. Common smells:

- Test needs to mock five things → too many dependencies, or wrong dependencies.
- Test needs to peek at private state → behavior isn't observable through the interface.
- Test setup is longer than the test itself → constructor or factory is doing too much.

Treat test pain as design feedback. Fix the code, not the test.
