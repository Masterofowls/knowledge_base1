---
tags: [playwright, testing, snippet]
aliases: [page.route, route.fulfill, HAR, page.clock]
created: 2026-10-26
status: evergreen
---
# Playwright Network Mocking

```ts
// Stub a response
await page.route("**/api/products", (route) =>
  route.fulfill({ status: 200, json: [{ id: 1, name: "Pen", price: 1.5 }] }));

// Modify a real response
await page.route("**/api/me", async (route) => {
  const response = await route.fetch();
  const json = await response.json();
  await route.fulfill({ response, json: { ...json, plan: "pro" } });
});

// Simulate errors & slowness
await page.route("**/api/orders", (route) => route.fulfill({ status: 500, json: { detail: "boom" } }));
await page.route("**/api/search*", async (route) => { await new Promise(r => setTimeout(r, 3000)); await route.continue(); });
await page.route("**/*.{png,jpg}", (route) => route.abort());       // block images

// Record & replay
await page.routeFromHAR("fixtures/api.har", { url: "**/api/**", update: false });

// Inspect requests
const req = await page.waitForRequest("**/api/orders");
expect(req.postDataJSON()).toMatchObject({ items: [{ id: 1 }] });

// Control time
await page.clock.setFixedTime(new Date("2026-12-24T10:00:00"));
await page.clock.install(); await page.clock.fastForward("05:00");
```

> [!tip] Mock in E2E to test UI states that are hard to produce (errors, empty lists, slow responses) — but keep some tests against the real backend, or you're only testing the frontend.

## Related
- [[Test Doubles]] · [[Jest Module Mocking]] (MSW) · [[HTTP Status Codes]] · [[React Data Fetching]]
