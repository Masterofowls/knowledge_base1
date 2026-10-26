---
tags: [moc, supertest, testing]
aliases: [Supertest]
created: 2026-10-26
---
# Supertest MOC

> [!abstract] Scope
> Supertest — HTTP assertions for Node.js servers (Express, Koa, Fastify, NestJS, plain `http`), typically run under [[Jest MOC|Jest]] (or Vitest / Mocha).

- 🚀 [[Supertest Getting Started]] — install, first steps, learning checklist
- 🛠️ Tutorial: [[Tutorial - REST API with Express and TypeScript]]

- [[Supertest Basics]] — app vs URL, request chain
- [[Supertest App Structure]] — split `app` from `listen` so tests run in-process
- [[Supertest Assertions]] — `.expect()` forms, body/header checks
- [[Supertest Authentication and Agents]] — tokens, cookies, `request.agent`
- [[Supertest with a Database]] — isolation strategies

## Cross-links
- [[Express MOC]] — the framework most Supertest suites target
- [[API Testing Strategy]] · [[HTTP Status Codes]] · [[REST API Design]]
- Python counterparts: [[FastAPI Testing]], [[Django Testing]]
- Alternative: [[Playwright API Testing]] (against a running server)
