---
tags: [tutorial, typescript, javascript]
aliases: [TS migration tutorial]
created: 2026-10-26
level: intermediate
time: 2h
---
# Tutorial - Migrate JavaScript to TypeScript

> [!abstract] What you'll do
> Take a small, untyped JavaScript module (a shopping cart), migrate it to strict TypeScript step by step, and discover real bugs the compiler finds along the way.

**Notes used:** [[TypeScript MOC]], [[TS tsconfig]], [[TS Unions and Narrowing]], [[TS Utility Types]].

## Starting point
```js
// cart.js
export function createCart(currency) {
  return { currency, items: [], discount: null };
}

export function addItem(cart, product, qty) {
  const existing = cart.items.find((i) => i.product.id === product.id);
  if (existing) existing.qty += qty;
  else cart.items.push({ product, qty });
  return cart;
}

export function applyDiscount(cart, discount) {
  cart.discount = discount;   // { type: "percent", value: 10 } or { type: "fixed", amount: 5 }
}

export function total(cart) {
  let sum = cart.items.reduce((s, i) => s + i.product.price * i.qty, 0);
  if (cart.discount.type === "percent") sum -= sum * (cart.discount.value / 100);
  if (cart.discount.type === "fixed") sum -= cart.discount.value;
  return Math.max(sum, 0).toFixed(2);
}
```
Did you spot the bugs? The compiler will.

## Step 1 — Allow JS, check JS
```bash
npm i -D typescript
npx tsc --init
```
In `tsconfig.json`: `"allowJs": true, "checkJs": true, "strict": true, "noEmit": true`. Run `npx tsc`. Even without types, TS infers enough to flag some issues. Add JSDoc as an intermediate step if you can't rename yet:
```js
/** @param {string} currency */
export function createCart(currency) { ... }
```

## Step 2 — Rename and model the domain
Rename `cart.js` → `cart.ts` and define types first:
```ts
export type Product = { id: string; name: string; price: number };
export type CartItem = { product: Product; qty: number };
export type Discount =
  | { type: "percent"; value: number }
  | { type: "fixed"; amount: number };
export type Currency = "EUR" | "USD" | "GBP";
export type Cart = { currency: Currency; items: CartItem[]; discount: Discount | null };
```

## Step 3 — Annotate function boundaries and fix the errors
```ts
export function createCart(currency: Currency): Cart {
  return { currency, items: [], discount: null };
}

export function addItem(cart: Cart, product: Product, qty: number): Cart { /* unchanged body */ }

export function applyDiscount(cart: Cart, discount: Discount): void {
  cart.discount = discount;
}

export function total(cart: Cart): string {
  let sum = cart.items.reduce((s, i) => s + i.product.price * i.qty, 0);
  if (cart.discount.type === "percent") ...
```
Run `npx tsc`. Errors you should see:
1. **`'cart.discount' is possibly 'null'`** — the original code crashed when no discount was applied! 🐛
2. **`Property 'value' does not exist on type '{ type: "fixed"; amount: number }'`** — the fixed discount used the wrong field! 🐛

Fix with narrowing:
```ts
export function total(cart: Cart): string {
  let sum = cart.items.reduce((s, i) => s + i.product.price * i.qty, 0);
  const d = cart.discount;
  if (d) {
    switch (d.type) {
      case "percent": sum -= sum * (d.value / 100); break;
      case "fixed":   sum -= d.amount; break;
      default: { const _never: never = d; return _never; }
    }
  }
  return Math.max(sum, 0).toFixed(2);
}
```

## Step 4 — Go further: immutability and better return types
- Returning a `string` from `total` is a smell (callers do math on it). Return `number` and format at the edge: `formatMoney(amount, currency)`.
- Make functions pure — return new carts instead of mutating:
```ts
export function addItem(cart: Readonly<Cart>, product: Product, qty: number): Cart {
  if (!Number.isInteger(qty) || qty <= 0) throw new RangeError("qty must be a positive integer");
  const exists = cart.items.some((i) => i.product.id === product.id);
  return {
    ...cart,
    items: exists
      ? cart.items.map((i) => (i.product.id === product.id ? { ...i, qty: i.qty + qty } : i))
      : [...cart.items, { product, qty }],
  };
}
```

## Step 5 — Lock it in with tests
```ts
// cart.test.ts — assumes total() now returns a number (Step 4)
import { addItem, applyDiscount, createCart, total } from "./cart";
const pen = { id: "p1", name: "Pen", price: 2.5 };

test("total without discount", () => {
  expect(total(addItem(createCart("EUR"), pen, 2))).toBe(5);
});
test("fixed discount never goes negative", () => {
  const cart = addItem(createCart("EUR"), pen, 1);
  applyDiscount(cart, { type: "fixed", amount: 10 });
  expect(total(cart)).toBe(0);
});
```

## Migration strategy for real projects
1. `allowJs` + `checkJs: false`, add `tsconfig`, get the build working.
2. Rename leaf modules (utils, types) first; work up toward entry points.
3. Turn on `strict` early; use `// @ts-expect-error` with a TODO rather than `any`.
4. Validate external data at runtime ([[TS Runtime Validation]]).
5. Track `any` count in CI (`typescript-coverage-report`) and drive it down.

## Related
- [[Tutorials MOC]] · [[TS Basic Types]] · [[Type Systems Compared]]
