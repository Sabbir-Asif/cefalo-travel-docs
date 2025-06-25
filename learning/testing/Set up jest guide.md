# Setting Up Unit Testing in a Node.js + Express + TypeScript Project with Jest

This guide will walk you through setting up unit testing with Jest in a Node.js + Express project written in TypeScript. All configuration files and test files will be in TypeScript, and each step will be explained to provide clear understanding.

---

## Prerequisites

Before you begin, ensure you have:

* A Node.js + Express project using TypeScript
* Basic understanding of TypeScript and Express
* Node.js and npm installed on your machine

---

## 1. Installing Dependencies

To run Jest tests on TypeScript code, you need some additional packages beyond Jest itself:

```bash
npm install --save-dev jest ts-jest @types/jest
```

**What these packages do:**

* `jest`: The testing framework that runs your tests.
* `ts-jest`: A transformer that allows Jest to understand and compile TypeScript code on the fly.
* `@types/jest`: TypeScript type definitions so that TypeScript knows about Jest's global functions like `describe`, `test`, and `expect`.

If your project uses ECMAScript Modules (indicated by `"type": "module"` in your `package.json`), Jest requires additional support:

```bash
npm install --save-dev jest-environment-node
```

This package provides a Node.js environment for Jest compatible with ESM.

---

## 2. Creating a Jest Configuration in TypeScript

You need to tell Jest how to handle your TypeScript files and your module system (CommonJS vs ESM).

Create a file named `jest.config.ts` at the root of your project.

### Why use a TypeScript config?

* Keeps configuration type-safe and consistent with your project
* Easier to maintain and update as your project evolves

---

### 2.1 For ESM projects (`"type": "module"` in `package.json`)

```ts
import type { Config } from '@jest/types';

// Jest configuration object with typing
const config: Config.InitialOptions = {
  // Use ts-jest preset that supports ES modules
  preset: 'ts-jest/presets/default-esm',

  // Node environment simulates Node.js runtime
  testEnvironment: 'node',

  // Treat .ts files as ES modules
  extensionsToTreatAsEsm: ['.ts'],

  // Glob pattern to locate test files
  testMatch: ['**/tests/**/*.test.ts'],

  // Prevent automatic transforms as we use ts-jest preset
  transform: {},

  // Fixes module resolution for ESM imports ending with .js in TS files
  moduleNameMapper: {
    '^(\\.{1,2}/.*)\\.js$': '$1',
  },
};

export default config;
```

**Explanation:**

* `preset: 'ts-jest/presets/default-esm'`: Tells Jest to use the `ts-jest` preset that supports ES modules.
* `testEnvironment: 'node'`: Runs tests in a Node.js environment, necessary for backend code.
* `extensionsToTreatAsEsm`: Ensures Jest treats `.ts` files as ESM modules.
* `testMatch`: Pattern that matches test files inside the `tests` folder with `.test.ts` extension.
* `transform: {}`: Disables additional transforms because the preset already handles TypeScript.
* `moduleNameMapper`: Fixes import paths that end with `.js` to work correctly in an ESM context.

---

### 2.2 For CommonJS projects (`"type": "commonjs"` or no `"type"` field)

```ts
import type { Config } from '@jest/types';

const config: Config.InitialOptions = {
  // Use ts-jest preset for CommonJS
  preset: 'ts-jest',

  testEnvironment: 'node',

  testMatch: ['**/tests/**/*.test.ts'],
};

export default config;
```

**Explanation:**

* This is a simpler config for projects using CommonJS modules.
* `preset: 'ts-jest'` enables TypeScript support.
* `testEnvironment` and `testMatch` as before.

---

## 3. Updating Your `tsconfig.json`

TypeScript needs to understand the Jest environment and include your test files.

Here is an example `tsconfig.json` configuration:

```json
{
  "compilerOptions": {
    "target": "ES2020",                     // Modern JS features, compatible with Node.js 14+
    "module": "ES2022",                     // Use ES modules; adjust if using CommonJS
    "moduleResolution": "Node",             // How modules are resolved
    "esModuleInterop": true,                 // Allows default import from CommonJS modules
    "strict": true,                          // Enables strict type checking
    "outDir": "./dist",                      // Compiled output folder
    "rootDir": "./src",                      // Source code root folder
    "types": ["jest"]                        // Include Jest typings globally
  },
  "include": ["src/**/*.ts", "tests/**/*.test.ts"] // Include source and tests
}
```

**Explanation:**

* `"types": ["jest"]`: Makes Jest global functions available in your TypeScript files without import.
* `"include"` includes both your source and test files so TypeScript compiles them.
* Adjust `"module"` and `"target"` based on your Node.js version and module system.

---

## 4. Suggested Project Structure

Organizing your source code and tests in a consistent way helps maintain clarity.

```
project-root/
├── src/
│   ├── app.ts
│   ├── controllers/
│   │   └── userController.ts
│   ├── services/
│   │   └── userService.ts
│   └── repositories/
│       └── userRepository.ts
├── tests/
│   └── userService.test.ts
├── jest.config.ts
└── tsconfig.json
```

**Why this structure?**

* Source files stay under `src`.
* Tests live in a parallel `tests` folder with `.test.ts` suffix.
* This clear separation avoids accidental mixing of test and production code.

You can also colocate tests next to the files they test using `.test.ts` or `.spec.ts` extensions if you prefer.

---

## 5. Writing Your First Unit Test in TypeScript

Suppose you have a simple function that returns a full name:

```ts
// src/services/userService.ts
export function getUserName(user: { firstName: string; lastName: string }): string {
  return `${user.firstName} ${user.lastName}`;
}
```

Create a corresponding test file:

```ts
// tests/userService.test.ts
import { getUserName } from '../src/services/userService';

describe('getUserName', () => {
  it('returns the full name of the user', () => {
    const user = { firstName: 'John', lastName: 'Doe' };
    const result = getUserName(user);
    expect(result).toBe('John Doe');
  });
});
```

**Explanation:**

* `describe` groups related tests.
* `it` defines a single test case.
* `expect` asserts that the function returns the expected result.
* Importing the function with relative path respects your folder structure.

---

## 6. Running Tests

Add a test script in your `package.json` for convenience:

```json
"scripts": {
  "test": "node --experimental-vm-modules node_modules/.bin/jest"
}
```

**Why the `--experimental-vm-modules` flag?**

* Jest relies on Node's experimental VM modules support to run tests with ES modules (`"type": "module"`).
* This flag enables that support so Jest can load your ESM/TS files correctly.

Run your tests with:

```bash
npm test
```

or

```bash
npx jest
```

