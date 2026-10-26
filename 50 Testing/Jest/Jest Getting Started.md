---
tags: [jest, guide]
aliases: [Learn Jest, Jest roadmap]
created: 2026-10-26
---
# Jest Getting Started

## 1. Install
```bash
npm i -D jest
# TypeScript: npm i -D ts-jest @types/jest && npx ts-jest config:init
# Vite projects: consider Vitest (same API): npm i -D vitest
npm pkg set scripts.test="jest"
```

## 2. First test
```js
// sum.js
function sum(...nums) { return nums.reduce((a, b) => a + b, 0); }
module.exports = { sum };

// sum.test.js
const { sum } = require("./sum");
describe("sum", () => {
  test("adds numbers", () => expect(sum(1, 2, 3)).toBe(6));
  test("empty is 0", () => expect(sum()).toBe(0));
});
```
```bash
npm test
npx jest --watch
npx jest --coverage
```

## 3. Learning checklist
- [ ] `describe` / `test` / `.each` → [[Jest Basics]]
- [ ] Matchers → [[Jest Matchers]]
- [ ] Setup & teardown → [[Jest Setup and Teardown]]
- [ ] Async tests → [[Jest Async Testing]]
- [ ] Mock functions & spies → [[Jest Mock Functions]]
- [ ] Module mocking & MSW → [[Jest Module Mocking]]
- [ ] Fake timers → [[Jest Fake Timers]]
- [ ] Snapshots → [[Jest Snapshot Testing]]
- [ ] Config (TS, coverage) → [[Jest Configuration]]
- [ ] React components → [[Jest Testing React Components]]
- [ ] HTTP APIs → [[Supertest Getting Started]]
- [ ] Practise: [[Tutorial - Async JavaScript Exercises]] (add tests), [[Tutorial - REST API with Express and TypeScript]]

## 4. Beginner mistakes
- Not awaiting async assertions (`expect(p).resolves` without `await`).
- `toBe` for objects/arrays (use `toEqual`).
- Mocks leaking between tests (set `restoreMocks: true`).
- Giant snapshots updated blindly with `-u`.
- Leaving `test.only` in committed code.

## Resources
- jestjs.io/docs/getting-started
- testing-library.com — for UI tests
- vitest.dev — if you're on Vite
