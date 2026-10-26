---
tags: [express, typescript, snippet]
aliases: [zod express, request validation]
created: 2026-10-26
status: evergreen
---
# Express Validation

> [!summary] TL;DR
> Express doesn't validate anything — `req.body` is whatever the client sent. Validate at the edge with a schema library and pass typed data to your services.

```ts
import { z } from "zod";
import type { RequestHandler } from "express";

const ItemCreate = z.object({
  name: z.string().trim().min(1).max(100),
  price: z.number().int().positive(),
  tags: z.array(z.string()).default([]),
});
const ListQuery = z.object({
  limit: z.coerce.number().int().min(1).max(100).default(20),   // coerce: query strings → numbers
  q: z.string().optional(),
});

// Reusable middleware
export const validate =
  (schemas: { body?: z.ZodType; query?: z.ZodType; params?: z.ZodType }): RequestHandler =>
  (req, res, next) => {
    for (const key of ["body", "query", "params"] as const) {
      const schema = schemas[key];
      if (!schema) continue;
      const result = schema.safeParse(req[key]);
      if (!result.success) {
        return res.status(422).json({ detail: "Validation failed", errors: result.error.issues });
      }
      res.locals[key] = result.data;          // req.query is read-only in Express 5
    }
    next();
  };

router.post("/", validate({ body: ItemCreate }), async (req, res) => {
  const data = res.locals.body as z.infer<typeof ItemCreate>;
  res.status(201).json(await items.create(data));
});

router.get("/", validate({ query: ListQuery }), async (req, res) => {
  res.json(await items.list(res.locals.query));
});
```

Alternatives: `express-validator`, Joi, Valibot, TypeBox + Ajv (emits JSON Schema → [[OpenAPI Schemas and Components|OpenAPI]]).

## Related
- [[TS Runtime Validation]] · [[FastAPI Pydantic Models]] — FastAPI does this automatically
