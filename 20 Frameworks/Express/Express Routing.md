---
tags: [express, snippet]
aliases: [express router, route params, express.Router]
created: 2026-10-26
status: evergreen
---
# Express Routing

```js
import { Router } from "express";

export const usersRouter = Router();

usersRouter.get("/", async (req, res) => {
  const { limit = "20", q } = req.query;        // query strings are strings!
  res.json(await users.list({ limit: Number(limit), q }));
});

usersRouter.get("/:id", async (req, res) => {
  const user = await users.get(Number(req.params.id));
  if (!user) return res.status(404).json({ detail: "User not found" });
  res.json(user);
});

usersRouter.post("/", async (req, res) => {
  const user = await users.create(req.body);
  res.status(201).location(`/users/${user.id}`).json(user);
});

usersRouter.patch("/:id", async (req, res) => { /* ... */ });
usersRouter.delete("/:id", async (req, res) => { await users.remove(Number(req.params.id)); res.sendStatus(204); });

// Chain methods on one path
usersRouter.route("/:id/avatar").get(getAvatar).put(uploadAvatar);

// Nested router with parent params
const ordersRouter = Router({ mergeParams: true });   // sees :userId
ordersRouter.get("/", (req, res) => res.json(orders.forUser(req.params.userId)));
usersRouter.use("/:userId/orders", ordersRouter);

// Mount
app.use("/users", usersRouter);
```

## Path patterns (Express 5)
| Pattern | Matches |
|---|---|
| `/users/:id` | `/users/42` → `req.params.id = "42"` |
| `/files/*path` | `/files/a/b.txt` → `req.params.path = ["a","b.txt"]` |
| `/:file{.:ext}` | `/report` and `/report.pdf` |
| `app.param("id", fn)` | preload/validate a param for every route using it |

> [!warning] Route order matters: `/users/me` must be registered before `/users/:id`.

## Related
- [[Express Request and Response]] · [[REST API Design]] · [[FastAPI Routing and Parameters]] · [[Django URLs and Routing]]
