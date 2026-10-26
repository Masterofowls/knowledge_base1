---
tags: [django, concept]
aliases: [models, ORM, ForeignKey, ManyToMany]
created: 2026-10-26
status: evergreen
---
# Django Models and ORM

```python
from django.db import models
from django.conf import settings

class Tag(models.Model):
    name = models.CharField(max_length=50, unique=True)
    def __str__(self): return self.name

class Post(models.Model):
    class Status(models.TextChoices):
        DRAFT = "draft"
        PUBLISHED = "published"

    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE, related_name="posts")
    title = models.CharField(max_length=200)
    slug = models.SlugField(unique=True)
    body = models.TextField()
    status = models.CharField(max_length=10, choices=Status, default=Status.DRAFT)
    tags = models.ManyToManyField(Tag, blank=True, related_name="posts")
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        ordering = ["-created_at"]
        indexes = [models.Index(fields=["status", "-created_at"])]
        constraints = [models.CheckConstraint(condition=~models.Q(title=""), name="title_not_empty")]

    def __str__(self): return self.title
```

## Relationships → SQL
| Django | SQL |
|---|---|
| `ForeignKey` | FK column (many-to-one) |
| `OneToOneField` | FK + unique |
| `ManyToManyField` | join table |

`on_delete`: `CASCADE`, `PROTECT`, `SET_NULL` (needs `null=True`), `SET_DEFAULT`, `RESTRICT`.

## CRUD
```python
post = Post.objects.create(author=u, title="Hi", slug="hi", body="...")
Post.objects.get(slug="hi")                  # DoesNotExist / MultipleObjectsReturned
Post.objects.filter(status="published", title__icontains="django")
post.title = "New"; post.save(update_fields=["title"])
Post.objects.filter(status="draft").update(status="published")   # single UPDATE
post.tags.add(tag)
u.posts.all()                                # reverse relation via related_name
```

> [!tip] `null=True` is a DB thing (column nullable); `blank=True` is a validation thing (form may be empty). For strings prefer `blank=True` with empty string rather than `null`.

## Related
- [[Django QuerySets and Performance]]
- [[Django Migrations]]
- [[SQL DDL and Constraints]] · [[SQL Joins]]
