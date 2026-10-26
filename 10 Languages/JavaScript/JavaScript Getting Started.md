---
tags: [javascript, guide]
aliases: [Learn JavaScript, JS roadmap]
created: 2026-10-26
---
# JavaScript Getting Started

## 1. Install
```powershell
winget install CoreyButler.NVMforWindows      # Windows (then: nvm install lts; nvm use lts)
```
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash && nvm install --lts   # macOS/Linux
node -v && npm -v
```
Editor: VS Code + ESLint + Prettier extensions. See [[Development Environment Setup]].

## 2. Hello world
```js
// hello.mjs
const greet = (name = "world") => `Hello, ${name}!`;
console.log(greet(process.argv[2]));
```
```bash
node hello.mjs Ada
node                     # REPL — try expressions interactively
```
In the browser: DevTools (`F12`) → **Console**.

## 3. Learning checklist
- [ ] Variables, scope, hoisting → [[JS Variables and Scope]]
- [ ] Types, truthiness, `===` → [[JS Data Types and Coercion]]
- [ ] Functions, arrow functions, closures → [[JS Functions and Closures]]
- [ ] Destructuring & spread → [[JS Destructuring and Spread]]
- [ ] Array methods → [[JS Array Methods]]
- [ ] `this` → [[JS this Keyword]]
- [ ] Classes & prototypes → [[JS Prototypes and Classes]]
- [ ] Errors → [[JS Error Handling]]
- [ ] Modules → [[JS Modules]]
- [ ] Event loop → [[JS Event Loop]]
- [ ] Promises & async/await → [[JS Promises and Async-Await]]
- [ ] Practise: [[Tutorial - Async JavaScript Exercises]]

## 4. Beginner mistakes
- Using `==` and `var`.
- Forgetting `await` (or `return`) on promises.
- Mutating arrays/objects you didn't create (`sort`, `push` on props/state).
- Assuming `fetch` throws on HTTP 404/500.
- Floating-point money math (`0.1 + 0.2`) — use integer cents.

## 5. Next steps
[[TypeScript Getting Started]] → [[React Getting Started]] or [[Express Getting Started]].

## Resources
- MDN JavaScript Guide — developer.mozilla.org/docs/Web/JavaScript/Guide
- javascript.info — modern tutorial, very thorough
- Node.js docs — nodejs.org/docs
