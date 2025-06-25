Writing a good unit test involves more than just checking if a function returns the expected result. A proper unit test should be **isolated, clear, meaningful, and maintainable**. Here's a structured method for writing effective unit tests:

---

### 1. Test One Thing Only

Each test case should focus on a **single behavior or scenario**. Don’t mix multiple assertions for different logic branches in one test.

```ts
// Good
test('adds two positive numbers', () => {
  expect(add(2, 3)).toBe(5);
});

// Bad: Testing multiple scenarios in one test
test('adds numbers', () => {
  expect(add(2, 3)).toBe(5);
  expect(add(-2, 3)).toBe(1);
});
```

---

### 2. Use Clear and Descriptive Names

Test names should describe what the test does and under what condition. Use a structure like:

**"FunctionName should do X when Y"**

```ts
test('calculateTax returns 10 when price is 100 and rate is 0.1', () => {
  expect(calculateTax(100, 0.1)).toBe(10);
});
```

---

### 3. Arrange-Act-Assert (AAA) Pattern

This helps keep test logic clean and readable.

```ts
test('should return correct full name', () => {
  // Arrange
  const firstName = 'John';
  const lastName = 'Doe';

  // Act
  const result = getFullName(firstName, lastName);

  // Assert
  expect(result).toBe('John Doe');
});
```

---

### 4. Avoid External Dependencies

Unit tests should not rely on:

* Databases
* APIs
* Filesystems
* Network calls

Instead, **mock or stub** these dependencies.

```ts
const db = { save: jest.fn() };

test('saveUser calls db.save with correct data', () => {
  const user = { name: 'Alice' };
  saveUser(user, db);
  expect(db.save).toHaveBeenCalledWith(user);
});
```

---

### 5. Cover Edge Cases and Error Handling

Don’t just test the “happy path.” Write tests for:

* Invalid input
* Boundary values
* Exceptions

```ts
test('divide throws error when dividing by zero', () => {
  expect(() => divide(10, 0)).toThrow('Cannot divide by zero');
});
```

---

### 6. Keep Tests Fast and Deterministic

Unit tests should run in milliseconds and produce the same result every time, regardless of system time, environment, or run order.

Avoid things like:

* Random values
* Real dates (`Date.now()`)
* Global state mutations

Use mocking or fixed data instead.

---

### 7. Use Setup and Teardown for Reusability (if needed)

If multiple tests share setup logic, move it to `beforeEach` or use test utility functions.

```ts
let cart;

beforeEach(() => {
  cart = new Cart();
});
```

---

### Summary Checklist

| Practice                 | Description                                   |
| ------------------------ | --------------------------------------------- |
| Test one thing           | Each test should validate a single behavior   |
| Use clear names          | Describe function, condition, and expectation |
| Use AAA pattern          | Arrange, Act, Assert to structure your tests  |
| Isolate dependencies     | Use mocks/stubs instead of real services      |
| Handle edge cases        | Test errors, boundaries, and unusual inputs   |
| Keep tests fast          | Avoid slow I/O or long-running operations     |
| Write maintainable tests | DRY, readable, and predictable                |

This approach helps you write unit tests that are easy to read, reliable, and useful in the long term.
