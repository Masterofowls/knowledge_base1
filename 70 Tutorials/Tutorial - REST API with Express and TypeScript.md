---
tags: [tutorial, express, typescript, supertest, jest]
aliases: [Express tutorial, Bookmarks API tutorial]
created: 2026-10-26
level: intermediate
time: 3h
---
# Tutorial - REST API with Express and TypeScript

> [!abstract] What you'll build
> A **Bookmarks API** (CRUD + search) in Express 5 + TypeScript, with Zod validation, centralised error handling, and an integration test suite using Supertest + Jest.

**Prerequisites:** Node 22+, basic [[JS Promises and Async-Await|async/await]], [[HTTP Methods]]. **Notes used:** [[Express MOC]], [[Supertest MOC]], [[Express Validation]].

## Step 1 — Project setup
```bash
mkdir bookmarks-api && cd bookmarks-api
npm init -y
npm i express zod
npm i -D typescript tsx @types/express @types/node jest ts-jest @types/jest supertest @types/supertest
npx tsc --init
```
Edit `tsconfig.json`:
```json
{
  "compilerOptions": {
    "target": "ES2022", "module": "commonjs", "moduleResolution": "node",
    "strict": true, "esModuleInterop": true, "skipLibCheck": true,
    "outDir": "dist", "rootDir": "."
  },
  "include": ["src", "tests"]
}
```
Add scripts to `package.json`:
```json
"scripts": {
  "dev": "tsx watch src/server.ts",
  "build": "tsc",
  "start": "node dist/src/server.js",
  "test": "jest"
},
"jest": { "preset": "ts-jest", "testEnvironment": "node" }
```

## Step 2 — Domain model & repository
```ts
// src/bookmarks.repo.ts
export type Bookmark = { id: number; url: string; title: string; tags: string[]; createdAt: string };
export type NewBookmark = Omit<Bookmark, "id" | "createdAt">;

export class BookmarkRepo {
  private items = new Map<number, Bookmark>();
  private nextId = 1;

  list(q?: string, tag?: string): Bookmark[] {
    return [...this.items.values()].filter(b =>
      (!q || b.title.toLowerCase().includes(q.toLowerCase())) && (!tag || b.tags.includes(tag)));
  }
  get(id: number) { return this.items.get(id); }
  create(data: NewBookmark): Bookmark {
    const b = { ...data, id: this.nextId++, createdAt: new Date().toISOString() };
    this.items.set(b.id, b);
    return b;
  }
  update(id: number, patch: Partial<NewBookmark>) {
    const existing = this.items.get(id);
    if (!existing) return undefined;
    const updated = { ...existing, ...patch };
    this.items.set(id, updated);
    return updated;
  }
  remove(id: number) { return this.items.delete(id); }
}
```
> [!info] An in-memory repo keeps the tutorial focused. Challenge 3 swaps it for Postgres — the routes won't change. That's the point of the repository layer ([[Express Project Structure]]).

## Step 3 — Errors and validation middleware
```ts
// src/errors.ts
export class HttpError extends Error {
  constructor(public status: number, message: string, public details?: unknown) { super(message); }
}
```
```ts
// src/validate.ts
import type { RequestHandler } from "express";
import type { ZodType } from "zod";

export const validateBody = (schema: ZodType): RequestHandler => (req, res, next) => {
  const result = schema.safeParse(req.body);
  if (!result.success) {
    return res.status(422).json({ detail: "Validation failed", errors: result.error.issues });
  }
  req.body = result.data;
  next();
};
```

## Step 4 — Routes
```ts
// src/bookmarks.routes.ts
import { Router } from "express";
import { z } from "zod";
import { BookmarkRepo } from "./bookmarks.repo";
import { HttpError } from "./errors";
import { validateBody } from "./validate";

const BookmarkCreate = z.object({
  url: z.string().url(),
  title: z.string().trim().min(1).max(200),
  tags: z.array(z.string().trim().min(1)).default([]),
});
const BookmarkUpdate = BookmarkCreate.partial();

export function bookmarksRouter(repo: BookmarkRepo) {
  const router = Router();

  router.get("/", (req, res) => {
    const { q, tag } = req.query as { q?: string; tag?: string };
    res.json(repo.list(q, tag));
  });

  router.get("/:id", (req, res) => {
    const b = repo.get(Number(req.params.id));
    if (!b) throw new HttpError(404, "Bookmark not found");
    res.json(b);
  });

  router.post("/", validateBody(BookmarkCreate), (req, res) => {
    const b = repo.create(req.body);
    res.status(201).location(`/bookmarks/${b.id}`).json(b);
  });

  router.patch("/:id", validateBody(BookmarkUpdate), (req, res) => {
    const b = repo.update(Number(req.params.id), req.body);
    if (!b) throw new HttpError(404, "Bookmark not found");
    res.json(b);
  });

  router.delete("/:id", (req, res) => {
    if (!repo.remove(Number(req.params.id))) throw new HttpError(404, "Bookmark not found");
    res.sendStatus(204);
  });

  return router;
}
```

## Step 5 — App factory and server
```ts
// src/app.ts
import express, { type ErrorRequestHandler } from "express";
import { BookmarkRepo } from "./bookmarks.repo";
import { bookmarksRouter } from "./bookmarks.routes";
import { HttpError } from "./errors";

export function createApp(repo = new BookmarkRepo()) {
  const app = express();
  app.use(express.json({ limit: "100kb" }));
  app.get("/health", (_req, res) => res.json({ status: "ok" }));
  app.use("/bookmarks", bookmarksRouter(repo));
  app.use((req, res) => res.status(404).json({ detail: `No route ${req.method} ${req.path}` }));

  const onError: ErrorRequestHandler = (err, _req, res, _next) => {
    if (err instanceof HttpError) return res.status(err.status).json({ detail: err.message });
    if (err.type === "entity.parse.failed") return res.status(400).json({ detail: "Malformed JSON" });
    console.error(err);
    res.status(500).json({ detail: "Internal Server Error" });
  };
  app.use(onError);
  return app;
}
```
```ts
// src/server.ts
import { createApp } from "./app";
const port = Number(process.env.PORT ?? 3000);
createApp().listen(port, () => console.log(`http://localhost:${port}`));
```

> [!check] Checkpoint
> ```bash
> npm run dev
> curl -s localhost:3000/health
> curl -s --json '{"url":"https://expressjs.com","title":"Express","tags":["node"]}' localhost:3000/bookmarks
> curl -s "localhost:3000/bookmarks?tag=node"
> curl -s -i --json '{"url":"nope"}' localhost:3000/bookmarks     # → 422
> ```

## Step 6 — Integration tests with Supertest
```ts
// tests/bookmarks.test.ts
import request from "supertest";
import { createApp } from "../src/app";

const valid = { url: "https://example.com", title: "Example", tags: ["demo"] };

describe("Bookmarks API", () => {
  let app: ReturnType<typeof createApp>;
  beforeEach(() => { app = createApp(); });          // fresh in-memory repo per test

  it("creates a bookmark", async () => {
    const res = await request(app).post("/bookmarks").send(valid).expect(201);
    expect(res.body).toMatchObject({ id: 1, ...valid });
    expect(res.headers.location).toBe("/bookmarks/1");
  });

  it("rejects invalid input with 422", async () => {
    const res = await request(app).post("/bookmarks").send({ url: "nope" }).expect(422);
    expect(res.body.errors.map((e: { path: string[] }) => e.path[0])).toEqual(expect.arrayContaining(["url", "title"]));
  });

  it("filters by tag", async () => {
    await request(app).post("/bookmarks").send(valid);
    await request(app).post("/bookmarks").send({ ...valid, tags: ["other"] });
    const res = await request(app).get("/bookmarks").query({ tag: "demo" }).expect(200);
    expect(res.body).toHaveLength(1);
  });

  it("updates and deletes", async () => {
    await request(app).post("/bookmarks").send(valid);
    await request(app).patch("/bookmarks/1").send({ title: "Renamed" }).expect(200)
      .expect((r) => expect(r.body.title).toBe("Renamed"));
    await request(app).delete("/bookmarks/1").expect(204);
    await request(app).get("/bookmarks/1").expect(404);
  });

  it("returns 400 on malformed JSON", () =>
    request(app).post("/bookmarks").set("Content-Type", "application/json").send("{bad").expect(400));
});
```
```bash
npm test
```

> [!check] Checkpoint — all 5 tests pass. Break something (e.g. return 200 instead of 201) and watch the right test fail.

## 🏋️ Challenges
1. Add pagination `?limit=&offset=` with Zod query validation and an `X-Total-Count` header.
2. Add `POST /auth/login` returning a JWT and protect write routes ([[Express Authentication]]). Test 401/403.
3. Replace `BookmarkRepo` with a Postgres or Prisma implementation behind the same interface ([[Express Database Access]]).
4. Add `helmet`, `cors` and rate limiting ([[Express Security]]).
5. Write an OpenAPI spec for this API and validate responses against it ([[Tutorial - Design-First API with OpenAPI]]).

> [!success]- Solution sketch: challenge 1
> ```ts
> const ListQuery = z.object({
>   q: z.string().optional(),
>   tag: z.string().optional(),
>   limit: z.coerce.number().int().min(1).max(100).default(20),
>   offset: z.coerce.number().int().min(0).default(0),
> });
> router.get("/", (req, res) => {
>   const parsed = ListQuery.safeParse(req.query);
>   if (!parsed.success) return res.status(422).json({ errors: parsed.error.issues });
>   const { q, tag, limit, offset } = parsed.data;
>   const all = repo.list(q, tag);
>   res.set("X-Total-Count", String(all.length)).json(all.slice(offset, offset + limit));
> });
> ```

## Related
- [[Tutorials MOC]] · [[Express vs FastAPI]] · [[Tutorial - FastAPI CRUD Service with pytest]] (same API, Python)
