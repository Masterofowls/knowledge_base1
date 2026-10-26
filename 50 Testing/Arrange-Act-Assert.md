---
tags: [testing, concept]
aliases: [AAA, Given-When-Then]
created: 2026-10-26
status: evergreen
---
# Arrange-Act-Assert

Structure every test in three visible blocks:

```python
def test_applies_discount_for_vip():
    # Arrange
    cart = Cart(customer=Customer(vip=True))
    cart.add(Item("pen", price=100), qty=2)

    # Act
    total = cart.total()

    # Assert
    assert total == 180
```

```ts
test("applies discount for VIP", () => {
  // Given
  const cart = new Cart({ vip: true });
  cart.add({ name: "pen", price: 100 }, 2);
  // When
  const total = cart.total();
  // Then
  expect(total).toBe(180);
});
```

Guidelines:
- **One behaviour per test** (multiple asserts about one outcome are fine).
- Test name = the behaviour/spec: `test_rejects_expired_token`, not `test_token_2`.
- Move noisy Arrange into fixtures/factories ([[pytest Fixtures]], [[Jest Setup and Teardown]], [[Playwright Fixtures]]).
- No logic (loops/ifs) in tests — parametrize instead ([[pytest Parametrize]], `test.each`).

## Related
- [[Testing Pyramid]] · [[Test-Driven Development]]
