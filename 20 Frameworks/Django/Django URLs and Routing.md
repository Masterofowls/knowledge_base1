---
tags: [django, snippet]
aliases: [urls.py, path, URLconf, reverse]
created: 2026-10-26
status: evergreen
---
# Django URLs and Routing

```python
# config/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path("admin/", admin.site.urls),
    path("blog/", include("blog.urls")),
    path("api/", include("api.urls")),
]

# blog/urls.py
from django.urls import path
from . import views

app_name = "blog"
urlpatterns = [
    path("", views.PostListView.as_view(), name="list"),
    path("<slug:slug>/", views.post_detail, name="detail"),
    path("archive/<int:year>/", views.archive, name="archive"),
]
```

Converters: `str`, `int`, `slug`, `uuid`, `path`.

## Reverse URLs — never hard-code paths
```python
from django.urls import reverse
reverse("blog:detail", kwargs={"slug": post.slug})
```
```django
<a href="{% url 'blog:detail' slug=post.slug %}">{{ post.title }}</a>
```

## Related
- [[Django Views]]
- [[REST API Design]] — resource naming
