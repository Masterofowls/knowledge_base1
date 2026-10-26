---
tags: [jest, testing, snippet]
aliases: [jest.config, ts-jest, babel-jest, swc jest]
created: 2026-10-26
status: evergreen
---
# Jest Configuration

```ts
// jest.config.ts
import type { Config } from "jest";

const config: Config = {
  testEnvironment: "node",                    // "jsdom" for DOM/React (install jest-environment-jsdom)
  transform: { "^.+\\.(t|j)sx?$": "@swc/jest" },   // or ["ts-jest", {...}]
  roots: ["<rootDir>/src", "<rootDir>/tests"],
  moduleNameMapper: {
    "^@/(.*)$": "<rootDir>/src/$1",           // match tsconfig paths
    "\\.(css|scss)$": "identity-obj-proxy",
  },
  setupFilesAfterEnv: ["<rootDir>/tests/setup.ts"],
  clearMocks: true,
  restoreMocks: true,
  collectCoverageFrom: ["src/**/*.{ts,tsx}", "!src/**/*.d.ts"],
  coverageThreshold: { global: { branches: 80, lines: 85 } },
  testTimeout: 10_000,
  // Multiple environments in one repo:
  // projects: [{ displayName: "api", testEnvironment: "node" }, { displayName: "web", testEnvironment: "jsdom" }],
};
export default config;
```

| Transformer | Pros | Cons |
|---|---|---|
| `ts-jest` | type-checks during tests | slower |
| `@swc/jest` / `babel-jest` | very fast | no type-check → run `tsc --noEmit` separately |

```json
// package.json
"scripts": {
  "test": "jest",
  "test:watch": "jest --watch",
  "test:ci": "jest --ci --coverage --reporters=default --reporters=jest-junit"
}
```

## Related
- [[TS tsconfig]] · [[JS Modules]] · [[Code Coverage]]
