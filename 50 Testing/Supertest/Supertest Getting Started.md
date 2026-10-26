---
tags: [supertest, guide]
aliases: [Learn Supertest]
created: 2026-10-26
---
# Supertest Getting Started

## 1. Install
```bash
npm i -D supertest jest
# TypeScript: npm i -D @types/supertest ts-jest @types/jest
```

## 2. First test
```js
// app.js — export the app, don't listen here
const express = require("express");
const app = express();
app.get("/health", (req, res) => res.json({ status: "ok" }));
module.exports = app;

// app.test.js
const request = require("supertest");
const app = require("./app");

test("GET /health", async () => {
  const res = await request(app).get("/health").expect(200).expect("Content-Type", /json/);
  expect(res.body).toEqual({ status: "ok" });
});
```
```bash
npx jest
```

## 3. Learning checklist
- [ ] Request chain & basics → [[Supertest Basics]]
- [ ] App vs server split → [[Supertest App Structure]]
- [ ] Assertions → [[Supertest Assertions]]
- [ ] Auth & cookie agents → [[Supertest Authentication and Agents]]
- [ ] Real database tests → [[Supertest with a Database]]
- [ ] What to test → [[API Testing Strategy]]
- [ ] Practise: [[Tutorial - REST API with Express and TypeScript]]

## 4. Beginner mistakes
- Importing a module that calls `app.listen()` → port conflicts and "Jest did not exit".
- Forgetting to `await` the request.
- Only testing happy paths — test 400/401/403/404/409/422 too.
- Shared DB state between tests.

## Resources
- github.com/ladjs/supertest — README is the documentation
