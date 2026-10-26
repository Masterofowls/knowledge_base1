---
tags: [django, guide]
aliases: [Learn Django, Django roadmap]
created: 2026-10-26
---
# Django Getting Started

## 1. Install
```bash
uv init mysite && cd mysite
uv add django
uv run django-admin startproject config .
uv run python manage.py migrate
uv run python manage.py createsuperuser
uv run python manage.py runserver        # http://localhost:8000 and /admin
```

## 2. Hello view
```bash
uv run python manage.py startapp pages
```
```python
# pages/views.py
from django.http import HttpResponse
def home(request):
    return HttpResponse("Hello Django")

# config/urls.py
from pages.views import home
urlpatterns = [path("admin/", admin.site.urls), path("", home)]
```
Add `"pages"` to `INSTALLED_APPS`.

## 3. Learning checklist
- [ ] Projects, apps, manage.py → [[Django Project Structure]]
- [ ] Models & ORM → [[Django Models and ORM]]
- [ ] Migrations → [[Django Migrations]]
- [ ] URLs → [[Django URLs and Routing]]
- [ ] Views (FBV & CBV) → [[Django Views]]
- [ ] Templates → [[Django Templates]]
- [ ] Forms → [[Django Forms]]
- [ ] Admin → [[Django Admin]]
- [ ] Settings & middleware → [[Django Settings and Middleware]]
- [ ] Auth & custom user model → [[Django Authentication]]
- [ ] QuerySet performance (N+1) → [[Django QuerySets and Performance]]
- [ ] REST APIs with DRF → [[Django REST Framework]]
- [ ] Testing → [[Django Testing]]
- [ ] Practise: [[Tutorial - Django Blog with REST API]]

## 4. Beginner mistakes
- Not creating a custom user model at the start of the project.
- Forgetting `makemigrations` after model changes (or editing applied migrations).
- N+1 queries in templates — use `select_related` / `prefetch_related`.
- `DEBUG = True` and hard-coded `SECRET_KEY` in production.
- Business logic scattered in views — put it in models/services.

## Resources
- Official tutorial — docs.djangoproject.com/en/stable/intro/tutorial01
- Django Girls tutorial — tutorial.djangogirls.org
- DRF tutorial — django-rest-framework.org/tutorial/quickstart
- Classy Class-Based Views — ccbv.co.uk
