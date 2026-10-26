---
tags: [django, concept]
aliases: [django auth, custom user model, permissions]
created: 2026-10-26
status: evergreen
---
# Django Authentication

> [!summary] TL;DR
> Django ships sessions-based auth: `User` model, password hashing, login/logout views, permissions & groups. **Start every project with a custom user model.**

```python
# accounts/models.py
from django.contrib.auth.models import AbstractUser
class User(AbstractUser):
    email = models.EmailField(unique=True)

# settings.py
AUTH_USER_MODEL = "accounts.User"
LOGIN_REDIRECT_URL = "/"
```

```python
# urls.py — built-in login/logout/password reset
path("accounts/", include("django.contrib.auth.urls")),
```

```python
from django.contrib.auth import authenticate, login
user = authenticate(request, username=u, password=p)
if user: login(request, user)

request.user.is_authenticated
request.user.has_perm("blog.change_post")
```

Protecting views: `@login_required`, `@permission_required("blog.add_post")`, `LoginRequiredMixin`, `PermissionRequiredMixin`.

For APIs: session auth (same-origin SPAs), or tokens/JWT via [[Django REST Framework]] (`djangorestframework-simplejwt`).

## Related
- [[Authentication Schemes]]
- [[Django Admin]]
