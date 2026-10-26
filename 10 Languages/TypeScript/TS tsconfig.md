---
tags: [typescript, snippet]
aliases: [tsconfig.json, strict mode]
created: 2026-10-26
status: evergreen
---
# TS tsconfig

A solid modern baseline:

```jsonc
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",            // or "ESNext" + "moduleResolution": "Bundler" for Vite/Next
    "moduleResolution": "NodeNext",
    "strict": true,                  // ⭐ enables all strict checks
    "noUncheckedIndexedAccess": true,// arr[i] is T | undefined
    "exactOptionalPropertyTypes": true,
    "noImplicitOverride": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "isolatedModules": true,         // needed by esbuild/swc/Vite
    "verbatimModuleSyntax": true,
    "jsx": "react-jsx",
    "outDir": "dist",
    "sourceMap": true,
    "declaration": true
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

## What `strict` turns on
`noImplicitAny`, `strictNullChecks`, `strictFunctionTypes`, `strictBindCallApply`, `strictPropertyInitialization`, `noImplicitThis`, `alwaysStrict`, `useUnknownInCatchVariables`.

## Commands
```bash
npx tsc --noEmit          # type-check only (CI)
npx tsc --watch
npx tsc --showConfig      # effective config
node --experimental-strip-types app.ts   # Node 22.6+ runs TS directly (types erased)
```

## Related
- [[JS Modules]] — ESM/CJS interplay
- [[Jest Configuration]] — `ts-jest` / `@swc/jest`
