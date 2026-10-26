---
tags: [express, security, snippet]
aliases: [jwt express, passport, express-session]
created: 2026-10-26
status: evergreen
---
# Express Authentication

## JWT (stateless APIs)
```ts
import jwt from "jsonwebtoken";
import argon2 from "argon2";

router.post("/auth/login", validate({ body: LoginBody }), async (req, res) => {
  const { email, password } = res.locals.body;
  const user = await users.byEmail(email);
  if (!user || !(await argon2.verify(user.passwordHash, password))) {
    return res.status(401).json({ detail: "Invalid credentials" });
  }
  const accessToken = jwt.sign({ sub: String(user.id), role: user.role }, process.env.JWT_SECRET!, {
    expiresIn: "15m",
    algorithm: "HS256",
  });
  res.json({ accessToken, tokenType: "Bearer" });
});

export const authenticate: RequestHandler = (req, res, next) => {
  const header = req.get("Authorization") ?? "";
  const [scheme, token] = header.split(" ");
  if (scheme !== "Bearer" || !token) {
    return res.status(401).set("WWW-Authenticate", "Bearer").json({ detail: "Missing token" });
  }
  try {
    const payload = jwt.verify(token, process.env.JWT_SECRET!, { algorithms: ["HS256"] }) as jwt.JwtPayload;
    req.user = { id: Number(payload.sub), role: payload.role };
    next();
  } catch {
    res.status(401).json({ detail: "Invalid or expired token" });
  }
};
```

## Sessions (server-rendered / same-site SPA)
```ts
import session from "express-session";
import { RedisStore } from "connect-redis";

app.set("trust proxy", 1);
app.use(session({
  store: new RedisStore({ client: redis }),
  secret: process.env.SESSION_SECRET!,
  resave: false,
  saveUninitialized: false,
  cookie: { httpOnly: true, secure: true, sameSite: "lax", maxAge: 8 * 3600_000 },
}));
// login: req.session.userId = user.id;   logout: req.session.destroy(...)
```

Libraries: **Passport.js** (strategies for OAuth providers), Auth.js, Lucia-style DIY, or an external IdP (Auth0, Cognito, Keycloak).

> [!warning] Hash passwords with argon2/bcrypt, never store plain or SHA. Keep secrets in env vars. Short-lived access tokens + refresh tokens (httpOnly cookie).

## Related
- [[Authentication Schemes]] · [[Supertest Authentication and Agents]] · [[FastAPI Authentication]]
