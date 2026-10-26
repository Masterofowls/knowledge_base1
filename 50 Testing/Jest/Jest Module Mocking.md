---
tags: [jest, testing, concept]
aliases: [jest.mock, manual mocks, __mocks__]
created: 2026-10-26
status: evergreen
---
# Jest Module Mocking

> [!summary] TL;DR
> `jest.mock(path)` replaces a module for the whole test file. Calls are **hoisted** above imports by babel-jest/ts-jest.

```ts
import { sendEmail } from "../src/email";
import { signup } from "../src/signup";

jest.mock("../src/email");                          // automock: all exports → jest.fn()
const mockedSend = jest.mocked(sendEmail);          // typed

test("signup sends welcome email", async () => {
  mockedSend.mockResolvedValue(undefined);
  await signup("ada@example.com");
  expect(mockedSend).toHaveBeenCalledWith("ada@example.com", expect.stringContaining("Welcome"));
});
```

```ts
// Factory: control the shape; keep some real exports
jest.mock("../src/config", () => ({
  ...jest.requireActual("../src/config"),
  featureFlags: { newCheckout: true },
}));

// Node modules / 3rd party
jest.mock("axios");

// Per-test variation
jest.isolateModules(() => { const mod = require("../src/config"); /* fresh copy */ });
```

Manual mocks: `__mocks__/axios.ts` next to `node_modules`, or `src/__mocks__/email.ts` next to the module.

> [!warning] ESM: `jest.mock` hoisting doesn't work in native ESM mode — use `jest.unstable_mockModule` + dynamic `import()`. Prefer dependency injection or **MSW** for HTTP to avoid module mocking altogether.

## HTTP with MSW (mock at the network layer)
```ts
import { http, HttpResponse } from "msw";
import { setupServer } from "msw/node";
const server = setupServer(http.get("/api/users/:id", ({ params }) => HttpResponse.json({ id: params.id })));
beforeAll(() => server.listen()); afterEach(() => server.resetHandlers()); afterAll(() => server.close());
```

## Related
- [[Jest Mock Functions]] · [[JS Modules]] · [[pytest Mocking]]
