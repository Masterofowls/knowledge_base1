---
tags: [django, snippet]
aliases: [admin site, ModelAdmin]
created: 2026-10-26
status: evergreen
---
# Django Admin

```python
from django.contrib import admin
from .models import Post, Tag

class TagInline(admin.TabularInline):
    model = Post.tags.through
    extra = 1

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ["title", "author", "status", "created_at"]
    list_filter = ["status", "created_at", "tags"]
    search_fields = ["title", "body", "author__username"]
    prepopulated_fields = {"slug": ["title"]}
    list_select_related = ["author"]             # avoid N+1 in changelist
    autocomplete_fields = ["author"]
    date_hierarchy = "created_at"
    inlines = [TagInline]
    actions = ["publish"]

    @admin.action(description="Publish selected posts")
    def publish(self, request, queryset):
        updated = queryset.update(status=Post.Status.PUBLISHED)
        self.message_user(request, f"{updated} posts published")

admin.site.register(Tag)
```

> [!tip] The admin is an internal tool for staff, not a customer-facing UI.

## Related
- [[Django Models and ORM]] · [[Django Authentication]]
