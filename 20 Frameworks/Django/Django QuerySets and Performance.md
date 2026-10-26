---
tags: [django, concept]
aliases: [QuerySet, N+1, select_related, prefetch_related, F, Q]
created: 2026-10-26
status: evergreen
---
# Django QuerySets and Performance

> [!summary] TL;DR
> QuerySets are **lazy** — no SQL runs until you iterate, slice with a step, `len()`, `list()`, `bool()`, or print. Chaining builds one query. The #1 perf bug is **N+1 queries**.

## Lookups, Q, F
```python
from django.db.models import Q, F, Count, Avg, Sum

Post.objects.filter(created_at__year=2026, author__username__startswith="a")
Post.objects.filter(Q(status="published") | Q(author=request.user))
Post.objects.exclude(tags__name="draft")
Product.objects.update(stock=F("stock") - 1)         # atomic, done in SQL
Post.objects.values("status").annotate(n=Count("id"))   # GROUP BY
Author.objects.annotate(num_posts=Count("posts")).filter(num_posts__gt=5)
```

## N+1 and how to fix it
```python
# ❌ 1 query for posts + 1 per post for author
for p in Post.objects.all():
    print(p.author.username)

# ✅ JOIN (FK / OneToOne)
Post.objects.select_related("author")
# ✅ 2nd query with IN (...) (M2M / reverse FK)
Post.objects.prefetch_related("tags")
```

## Other tools
- `.only("id", "title")` / `.defer("body")` — fewer columns
- `.values_list("id", flat=True)` — no model instances
- `.exists()` instead of `if qs:`; `.count()` instead of `len(qs)`
- `.iterator(chunk_size=2000)` for huge result sets
- `bulk_create`, `bulk_update`
- `print(qs.query)` / `qs.explain()` — inspect SQL
- **django-debug-toolbar** to count queries per request

## Testing query counts
```python
def test_list_is_constant_queries(django_assert_num_queries, client):
    with django_assert_num_queries(3):
        client.get("/posts/")
```

## Related
- [[SQL Joins]] · [[SQL Indexes]] · [[SQL Aggregation and GROUP BY]]
- [[Django Testing]]
