---
tags: [typescript, snippet]
aliases: [Partial, Pick, Omit, Record, utility types]
created: 2026-10-26
status: evergreen
---
# TS Utility Types

```ts
interface User { id: number; name: string; email: string; role: "admin" | "user" }
```

| Utility | Result | Typical use |
|---|---|---|
| `Partial<User>` | all optional | PATCH payloads |
| `Required<T>` | all required | after defaults applied |
| `Readonly<T>` | all readonly | immutable state |
| `Pick<User, "id" \| "name">` | subset | DTOs / list views |
| `Omit<User, "id">` | minus keys | create payloads |
| `Record<"a" \| "b", number>` | object map | lookups |
| `Exclude<U, X>` / `Extract<U, X>` | filter unions | |
| `NonNullable<T>` | drop `null`/`undefined` | |
| `ReturnType<typeof fn>` | fn's return | derive types from code |
| `Parameters<typeof fn>` | arg tuple | wrappers |
| `Awaited<P>` | unwrap promise | `Awaited<ReturnType<typeof fetchUser>>` |

```ts
type CreateUser = Omit<User, "id">;
type UpdateUser = Partial<CreateUser>;
type RoleCounts = Record<User["role"], number>;
```

> [!tip] This mirrors REST payload design: POST body = `Omit<T, "id">`, PATCH body = `Partial<...>`. See [[REST API Design]] and [[FastAPI Pydantic Models]] for the Python side.

## Related
- [[TS Generics]]
- [[TS Mapped and Conditional Types]] — how these are built
