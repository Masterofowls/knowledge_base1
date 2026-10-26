---
tags: [django, concept]
aliases: [manage.py, django apps, startproject]
created: 2026-10-26
status: evergreen
---
# Django Project Structure

```bash
uv add django
uv run django-admin startproject config .     # project (settings, root urls)
uv run python manage.py startapp blog         # app (a reusable feature module)
uv run python manage.py runserver
```

```
.
├── manage.py
├── config/
│   ├── settings.py      # → [[Django Settings and Middleware]]
│   ├── urls.py          # root URLconf
│   ├── asgi.py / wsgi.py
└── blog/
    ├── models.py        # → [[Django Models and ORM]]
    ├── views.py         # → [[Django Views]]
    ├── urls.py
    ├── admin.py         # → [[Django Admin]]
    ├── forms.py
    ├── migrations/
    ├── templates/blog/
    └── tests.py
```

Register the app: `INSTALLED_APPS += ["blog"]`.

## MVT request flow
`Request → middleware → URLconf → view → (model ↔ DB) → template → Response → middleware`

## manage.py cheat sheet
| Command | Purpose |
|---|---|
| `makemigrations` / `migrate` | schema changes |
| `createsuperuser` | admin login |
| `shell` | REPL with Django loaded |
| `dbshell` | DB CLI |
| `test` | run tests |
| `collectstatic` | gather static files for prod |
| `check --deploy` | production readiness check |

## Related
- [[Django MOC]] · [[Python Virtual Environments and Packaging]]
