---
tags: [python, concept]
aliases: [generators, yield, iterators, itertools]
created: 2026-10-26
status: evergreen
---
# Python Generators and Iterators

> [!summary] TL;DR
> An **iterator** produces values one at a time via `__next__`. A **generator** is a function with `yield` that creates an iterator lazily — constant memory, values computed on demand.

```python
def read_lines(path):
    with open(path) as f:
        for line in f:
            yield line.rstrip("\n")

def chunked(iterable, size):
    batch = []
    for item in iterable:
        batch.append(item)
        if len(batch) == size:
            yield batch
            batch = []
    if batch:
        yield batch

errors = (l for l in read_lines("app.log") if "ERROR" in l)   # generator expression
for batch in chunked(errors, 100):
    send(batch)

def flatten(nested):
    for x in nested:
        yield from x            # delegate
```

## itertools highlights
`islice`, `chain`, `groupby`, `product`, `permutations`, `combinations`, `accumulate`, `pairwise` (3.10), `batched` (3.12).

> [!warning] Generators are single-use; iterating twice yields nothing the second time.

## Related
- [[Python Comprehensions]]
- [[pytest Fixtures]] — `yield` fixtures use generators for setup/teardown
- [[Python Context Managers]]
