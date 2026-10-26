---
tags: [testing, concept]
aliases: [TDD, red green refactor]
created: 2026-10-26
status: evergreen
---
# Test-Driven Development

**Red → Green → Refactor**
1. 🔴 Write a small failing test for the next behaviour.
2. 🟢 Write the *simplest* code that makes it pass.
3. 🔵 Refactor code and tests with the safety net green.

```python
# 🔴
def test_slugify_lowercases_and_hyphenates():
    assert slugify("Hello World") == "hello-world"

# 🟢
def slugify(s: str) -> str:
    return s.lower().replace(" ", "-")

# 🔴 next case
def test_slugify_strips_punctuation():
    assert slugify("Hi, there!") == "hi-there"
# ... and so on
```

Benefits: design pressure toward small, decoupled units; executable spec; regression net.
Where it shines: business logic, parsers, algorithms, bug fixes (write the failing reproduction first!).
Where it's awkward: exploratory UI work, spikes — write tests after, once the shape settles.

Variants: **BDD** (behaviour language, Given/When/Then), **outside-in / London school** (start from an acceptance test, mock collaborators).

## Related
- [[Arrange-Act-Assert]] · [[Testing Pyramid]]
