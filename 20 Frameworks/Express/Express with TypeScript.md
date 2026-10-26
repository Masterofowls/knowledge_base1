---
tags: [express, typescript, snippet]
aliases: [express ts, typed express]
created: 2026-10-26
status: evergreen
---
# Express with TypeScript

```bash
npm i express zod
npm i -D typescript @types/express @types/node tsx
npx tsc --init
```

```jsonc
// package.json
{
  "type": "module",
  "scripts": {
    "dev": "tsx watch src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "typecheck": "tsc --noEmit"
  }
}
```

```ts
// Typed handlers
import type { Request, Response, NextFunction, RequestHandler } from "express";

type Params = { id: string };
type Body = { name: string; price: number };
type ResBody = { id: number; name: string; price: number };

const createItem: RequestHandler<{}, ResBody, Body> = async (req, res) => {
  const item = await items.create(req.body);   // req.body: Body
  res.status(201).json(item);                   // must match ResBody
};

// Augment Request with your own fields
// src/types/express.d.ts
declare global {
  namespace Express {
    interface Request {
      user?: { id: number; role: "admin" | "user" };
      id?: string;
    }
  }
}
export {};
```

> [!tip] The generic type for `req.body` is a promise you make, not a check. Always validate at runtime too → [[Express Validation]].

## Related
- [[TS tsconfig]] · [[TS Generics]] · [[Tutorial - REST API with Express and TypeScript]]
