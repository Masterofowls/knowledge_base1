---
tags: [express, guide]
aliases: [Learn Express, Express roadmap]
created: 2026-10-26
---
# Express Getting Started

## 1. Install
```bash
mkdir my-api && cd my-api
npm init -y
npm pkg set type=module
npm i express
```

## 2. Hello world
```js
// server.js
import express from "express";
const app = express();
app.use(express.json());
app.get("/", (req, res) => res.json({ message: "Hello Express" }));
app.listen(3000, () => console.log("http://localhost:3000"));
```
```bash
node --watch server.js
curl localhost:3000
```

## 3. Learning checklist
- [ ] App & request lifecycle → [[Express Basics]]
- [ ] Routing & routers → [[Express Routing]]
- [ ] `req` / `res` API → [[Express Request and Response]]
- [ ] Middleware → [[Express Middleware]]
- [ ] Error handling → [[Express Error Handling]]
- [ ] Validation with Zod → [[Express Validation]]
- [ ] Project structure & app factory → [[Express Project Structure]]
- [ ] Database access → [[Express Database Access]]
- [ ] Authentication → [[Express Authentication]]
- [ ] Security hardening → [[Express Security]]
- [ ] TypeScript → [[Express with TypeScript]]
- [ ] Testing with Supertest → [[Supertest Getting Started]]
- [ ] Practise: [[Tutorial - REST API with Express and TypeScript]]

## 4. Beginner mistakes
- Forgetting `express.json()` → `req.body` is `undefined`.
- Sending two responses (`res.json` without `return`).
- Registering the error handler before the routes (must be last, 4 args).
- Trusting `req.body` without validation.
- Calling `app.listen()` in the file tests import.

## Resources
- expressjs.com — guide & API reference (check you're on the 5.x docs)
- "Node.js best practices" — github.com/goldbergyoni/nodebestpractices
