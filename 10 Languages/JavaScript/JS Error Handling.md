---
tags: [javascript, concept]
aliases: [try catch, JS exceptions]
created: 2026-10-26
status: evergreen
---
# JS Error Handling

```js
class ValidationError extends Error {
  constructor(field, message) {
    super(message);
    this.name = "ValidationError";
    this.field = field;
  }
}

try {
  riskyThing();
} catch (err) {
  if (err instanceof ValidationError) report(err.field);
  else throw err;                       // don't swallow unknown errors
} finally {
  cleanup();
}

// Error chaining (ES2022)
throw new Error("Failed to load user", { cause: err });
```

## Async errors
```js
// async/await → try/catch works
try { await fetchUser(); } catch (e) { /* ... */ }

// promises → .catch
fetchUser().catch(handle);

// Unhandled rejections crash Node ≥15 — always handle them
process.on("unhandledRejection", (r) => { console.error(r); process.exit(1); });
```

> [!warning] `fetch` does **not** reject on 4xx/5xx — check `res.ok`.
> ```js
> const res = await fetch(url);
> if (!res.ok) throw new Error(`HTTP ${res.status}`);
> ```
> See [[HTTP Status Codes]].

## Related
- [[JS Promises and Async-Await]]
- [[Python Exceptions]]
- [[Jest Async Testing]] — asserting rejections
