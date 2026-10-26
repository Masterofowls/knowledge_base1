---
tags: [express, concept]
aliases: [express architecture, layered architecture node]
created: 2026-10-26
status: evergreen
---
# Express Project Structure

> [!summary] TL;DR
> Keep HTTP concerns (routes, validation, status codes) separate from business logic (services) and data access (repositories). Build the app in a `createApp(deps)` factory so tests can inject fakes.

```
src/
├── app.ts                # createApp(deps): middleware + routers + error handler
├── server.ts             # reads config, builds deps, app.listen()
├── config.ts             # env parsing with zod
├── routes/
│   └── items.routes.ts   # HTTP: parse/validate → call service → map to response
├── services/
│   └── items.service.ts  # business rules, no req/res
├── repositories/
│   └── items.repo.ts     # SQL / Prisma
├── middleware/           # auth, validate, requestId, errorHandler
├── errors.ts
└── types/express.d.ts    # augment Request (req.user)
tests/
├── items.api.test.ts     # Supertest
└── items.service.test.ts # unit
```

Data flow: `route → service → repository → DB`, errors bubble up as typed `HttpError`/domain errors to the error middleware.

```ts
// config.ts — fail fast on bad configuration
import { z } from "zod";
export const config = z.object({
  NODE_ENV: z.enum(["development", "test", "production"]).default("development"),
  PORT: z.coerce.number().default(3000),
  DATABASE_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
}).parse(process.env);
```

Larger apps: feature folders (`src/items/{routes,service,repo}.ts`) scale better than layer folders. Frameworks that formalize this: **NestJS** (DI, modules), **Fastify** (schemas, plugins, faster).

## Related
- [[Supertest App Structure]] · [[Express with TypeScript]] · [[FastAPI Basics]] (layout) · [[Django Project Structure]]
