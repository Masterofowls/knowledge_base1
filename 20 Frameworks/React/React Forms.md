---
tags: [react, snippet]
aliases: [controlled components, forms]
created: 2026-10-26
status: evergreen
---
# React Forms

## Controlled inputs
```jsx
function SignupForm({ onSubmit }) {
  const [form, setForm] = useState({ email: "", password: "" });
  const update = (e) => setForm(f => ({ ...f, [e.target.name]: e.target.value }));

  return (
    <form onSubmit={(e) => { e.preventDefault(); onSubmit(form); }}>
      <label htmlFor="email">Email</label>
      <input id="email" name="email" type="email" value={form.email} onChange={update} />
      <label htmlFor="password">Password</label>
      <input id="password" name="password" type="password" value={form.password} onChange={update} />
      <button type="submit">Sign up</button>
    </form>
  );
}
```

## Uncontrolled + FormData (simpler; React 19 actions)
```jsx
function Search() {
  async function action(formData) {
    await search(formData.get("q"));
  }
  return <form action={action}><input name="q" /><button>Go</button></form>;
}
```

## At scale
**React Hook Form** + a schema resolver ([[TS Runtime Validation|Zod]]) — minimal re-renders, declarative validation.

> [!tip] Always associate `<label htmlFor>` with inputs: accessibility *and* it makes `getByLabelText` work in [[Jest Testing React Components]] and `getByLabel` in [[Playwright Locators]].

## Related
- [[React State and Props]]
