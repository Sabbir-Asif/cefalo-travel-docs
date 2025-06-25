A **unit test** is a type of software test that focuses on **testing a single "unit" of code in isolation**—typically a **function**, **method**, or **class**—to ensure it behaves as expected.

---

## What is a "Unit"?

* A **unit** is the **smallest testable part** of an application.
* In JavaScript/TypeScript, this is often:

  * A function (e.g., `calculateTax`)
  * A method of a class
  * A small module

---

## Characteristics of Unit Tests

| Feature        | Description                                                                                                                      |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| **Isolated**   | The unit is tested without external dependencies like databases, networks, or file systems. If needed, **mocks/stubs** are used. |
| **Fast**       | Since it runs in-memory and doesn’t interact with real I/O or services.                                                          |
| **Repeatable** | Same input always gives same result.                                                                                             |
| **Focused**    | Tests just one behavior or logic branch at a time.                                                                               |

---

## Example in JavaScript (with Jest)

Suppose you have a function:

```ts
// tax.js
export function calculateTax(price, rate) {
  return price * rate;
}
```

### Unit Test:

```ts
// tax.test.js
import { calculateTax } from './tax';

test('calculates tax correctly', () => {
  expect(calculateTax(100, 0.1)).toBe(10);
});
```

* This test checks **only one thing**: that `calculateTax` gives the correct result.
* It doesn't depend on any external systems, just pure logic.

---

## Why Are Unit Tests Important?

* **Catch bugs early** before integration.
* **Refactor code safely** with confidence.
* Serve as **documentation** for expected behavior.
* Allow **test-driven development (TDD)**.

---

## Unit Test vs Other Test Types

| Type                 | Scope                                      | Example                                                   |
| -------------------- | ------------------------------------------ | --------------------------------------------------------- |
| **Unit Test**        | One function/class                         | `calculateTax()` returns 10                               |
| **Integration Test** | Multiple units/components working together | `POST /checkout` hits DB, calculates tax, returns receipt |
| **End-to-End (E2E)** | Entire system, user flow                   | User signs up and gets welcome email                      |

---

> **In short**: A unit test checks that a single piece of logic works correctly in isolation—it's like testing a single Lego block before building the whole set.

# Why we need unit tests?

We do unit testing to ensure that individual pieces of code work correctly in isolation. This brings several key benefits to software development:

### 1. Catch Bugs Early

Unit tests help detect issues at the earliest possible stage—when a developer writes or modifies code. Fixing bugs early is faster and cheaper than catching them later during integration or in production.

### 2. Enable Safe Refactoring

When making changes to code, existing unit tests act as a safety net. If a test fails after a change, you know something broke. This encourages developers to improve code structure without fear of unintentionally introducing bugs.

### 3. Improve Code Quality and Design

Writing testable code often leads to better design. Code that's easy to unit test is usually modular, focused, and loosely coupled—qualities that make systems easier to maintain and scale.

### 4. Support Continuous Integration (CI)

Unit tests are a key part of automated testing pipelines. They run quickly and provide fast feedback in CI environments, allowing teams to detect problems immediately when pushing changes.

### 5. Serve as Living Documentation

Unit tests show how individual functions or components are expected to behave. For new developers joining a project, tests can help them understand the intended use and constraints of different parts of the codebase.

### 6. Reduce Manual Testing Effort

Automated unit tests replace the need to repeatedly test individual logic manually. This saves time and ensures consistent, repeatable test coverage.

### 7. Improve Developer Confidence

With a strong unit test suite, developers can make changes, add features, or perform upgrades with more confidence that they won't break existing functionality.

In summary, unit testing is a foundational practice in modern software development that improves reliability, maintainability, and developer productivity.
