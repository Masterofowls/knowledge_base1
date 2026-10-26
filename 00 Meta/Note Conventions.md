---
tags: [meta]
created: 2026-10-26
---
# Note Conventions

## Frontmatter
```yaml
---
tags: [python, concept]   # topic + note type
aliases: [Alt name]       # so [[Alt name]] resolves
created: 2026-10-26
status: seed | growing | evergreen
---
```

## Note types (tag)
- `moc` — map of content
- `guide` — Getting Started note (install, hello world, checklist)
- `tutorial` — step-by-step project (frontmatter also has `level` and `time`)
- `concept` — explanation of an idea
- `snippet` — copy-paste recipe / cheat sheet
- `comparison` — X vs Y
- `question` — open question to research

## Callouts used
> [!summary] TL;DR — top of each concept note

> [!tip] Practical advice

> [!warning] Pitfalls / gotchas

> [!example] Worked examples

## Naming
- Prefix with the technology when the name would collide: `JS Closures`, `Python Decorators`, `pytest Fixtures`.
- No dates in concept-note titles; dates are for daily notes.

## Templates
Insert with **Templates: Insert template** (`Ctrl+P`). Replace the `topic-tag` placeholder with the real topic tag (e.g. `python`).
