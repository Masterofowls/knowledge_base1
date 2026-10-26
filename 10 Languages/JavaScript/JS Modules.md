---
tags: [javascript, concept]
aliases: [ESM, CommonJS, import export, require]
created: 2026-10-26
status: evergreen
---
# JS Modules

> [!summary] TL;DR
> **ESM** (`import`/`export`) is the standard: static, async-loaded, tree-shakeable. **CommonJS** (`require`/`module.exports`) is Node's legacy, synchronous system. New code: ESM.

```js
// ESM — math.js
export const add = (a, b) => a + b;
export default function mul(a, b) { return a * b; }

// consumer
import mul, { add } from "./math.js";
import * as math from "./math.js";
const { heavy } = await import("./heavy.js");   // dynamic, code-splitting
```

```js
// CommonJS
const fs = require("node:fs");
module.exports = { add };
```

## Node: how a file is treated
| Signal | Module type |
|---|---|
| `.mjs` | ESM |
| `.cjs` | CommonJS |
| `.js` + `"type": "module"` in package.json | ESM |
| `.js` otherwise | CommonJS |

> [!warning] ESM gotchas in Node
> - No `__dirname` → use `import.meta.dirname` (Node 20.11+) or `fileURLToPath(import.meta.url)`.
> - Relative imports need the file extension: `./util.js`.
> - [[Jest MOC|Jest]] ESM support is still experimental; many projects transpile via `ts-jest`/`babel-jest`.

## Related
- [[TS tsconfig]] — `module` / `moduleResolution`
- [[Package Managers Compared]]
- [[Python Modules and Imports]]
