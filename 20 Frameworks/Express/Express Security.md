---
tags: [express, security, snippet]
aliases: [helmet, rate limiting, express hardening]
created: 2026-10-26
status: evergreen
---
# Express Security

```ts
import helmet from "helmet";
import cors from "cors";
import rateLimit from "express-rate-limit";

app.disable("x-powered-by");                      // helmet also does this
app.set("trust proxy", 1);                         // behind a load balancer → correct req.ip
app.use(helmet());                                 // CSP, HSTS, nosniff, frame options…
app.use(cors({ origin: ["https://app.example.com"], credentials: true }));
app.use(express.json({ limit: "100kb" }));         // cap body size
app.use("/auth", rateLimit({ windowMs: 15 * 60_000, limit: 20, standardHeaders: "draft-7" }));
```

## Checklist
- [ ] Validate every input ([[Express Validation]]); reject unknown fields.
- [ ] Parameterized queries only ([[SQL Injection and Parameterized Queries]]).
- [ ] Auth on every non-public route; authorize per resource (user owns order?).
- [ ] Secure cookies: `httpOnly`, `secure`, `sameSite`.
- [ ] CSRF protection if using cookie auth with forms.
- [ ] Don't leak stack traces/errors in production ([[Express Error Handling]]).
- [ ] `npm audit` / Dependabot / Renovate for dependency updates.
- [ ] Rate-limit login, signup, password reset.
- [ ] Set `NODE_ENV=production`.
- [ ] Log security events with request IDs, never log secrets/tokens.

## Related
- [[CORS]] · [[HTTP Headers]] (security headers) · [[Authentication Schemes]]
