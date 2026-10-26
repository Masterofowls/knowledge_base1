---
tags: [typescript, concept]
aliases: [Zod, runtime validation, schema validation]
created: 2026-10-26
status: growing
---
# TS Runtime Validation

> [!summary] TL;DR
> TypeScript types are erased at runtime. Anything crossing a trust boundary (HTTP bodies, `JSON.parse`, env vars, localStorage) must be **validated at runtime**. Schema libraries give you validation *and* the static type from one definition.

```ts
import { z } from "zod";

const UserSchema = z.object({
  id: z.number().int().positive(),
  email: z.string().email(),
  role: z.enum(["admin", "user"]).default("user"),
  tags: z.array(z.string()).optional(),
});
type User = z.infer<typeof UserSchema>;   // static type derived

const result = UserSchema.safeParse(await res.json());
if (!result.success) console.error(result.error.issues);
else useUser(result.data);                 // fully typed

// env validation
const Env = z.object({ DATABASE_URL: z.string().url(), PORT: z.coerce.number().default(3000) });
export const env = Env.parse(process.env);
```

Alternatives: Valibot (smaller), ArkType, TypeBox (emits JSON Schema), io-ts.

> [!tip] This is exactly what [[FastAPI Pydantic Models|Pydantic]] does for Python — one model = validation + types + [[OpenAPI Schemas and Components|OpenAPI schema]].

## Related
- [[TS Basic Types]] — why `as` is unsafe
- [[OpenAPI Tooling]] — generate schemas from a spec
