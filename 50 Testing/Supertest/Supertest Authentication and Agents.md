---
tags: [supertest, testing, snippet]
aliases: [request.agent, supertest cookies, bearer token supertest]
created: 2026-10-26
status: evergreen
---
# Supertest Authentication and Agents

## Bearer tokens
```ts
let token: string;
beforeAll(async () => {
  const res = await request(app).post("/auth/login").send({ email: "ada@example.com", password: "pw" }).expect(200);
  token = res.body.accessToken;
});

it("gets profile", async () => {
  await request(app).get("/me").set("Authorization", `Bearer ${token}`).expect(200);
  // or: .auth(token, { type: "bearer" })
});

it("rejects missing token", () => request(app).get("/me").expect(401));
it("forbids other users' orders", () =>
  request(app).get("/orders/123").auth(otherToken, { type: "bearer" }).expect(403));
```

## Cookie sessions — `request.agent` persists cookies
```ts
const agent = request.agent(app);
await agent.post("/login").type("form").send({ username: "ada", password: "pw" }).expect(302);
await agent.get("/dashboard").expect(200);          // session cookie sent automatically
```

## Generate tokens directly (skip the login endpoint)
```ts
import jwt from "jsonwebtoken";
const tokenFor = (user: { id: number; role: string }) =>
  jwt.sign({ sub: String(user.id), role: user.role }, process.env.JWT_SECRET!, { expiresIn: "5m" });
```

## Related
- [[Authentication Schemes]] · [[Playwright Authentication]] · [[FastAPI Testing]] (override `get_current_user`)
