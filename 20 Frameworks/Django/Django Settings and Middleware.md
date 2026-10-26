---
tags: [django, concept]
aliases: [settings.py, middleware, environment config]
created: 2026-10-26
status: evergreen
---
# Django Settings and Middleware

## Settings essentials
```python
import os
from pathlib import Path
BASE_DIR = Path(__file__).resolve().parent.parent

SECRET_KEY = os.environ["DJANGO_SECRET_KEY"]
DEBUG = os.environ.get("DJANGO_DEBUG") == "1"
ALLOWED_HOSTS = os.environ.get("DJANGO_ALLOWED_HOSTS", "").split(",")

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": os.environ["DB_NAME"], "USER": os.environ["DB_USER"],
        "PASSWORD": os.environ["DB_PASSWORD"], "HOST": os.environ.get("DB_HOST", "localhost"),
        "CONN_MAX_AGE": 60,
    }
}
STATIC_ROOT = BASE_DIR / "staticfiles"
```
Tools: `django-environ`, `pydantic-settings`, or split `settings/base.py, dev.py, prod.py`.

## Middleware (onion model)
```python
MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
    "django.contrib.sessions.middleware.SessionMiddleware",
    "django.middleware.common.CommonMiddleware",
    "django.middleware.csrf.CsrfViewMiddleware",
    "django.contrib.auth.middleware.AuthenticationMiddleware",
    "django.contrib.messages.middleware.MessageMiddleware",
    "django.middleware.clickjacking.XFrameOptionsMiddleware",
]
```
Order matters: request goes top→down, response bottom→up.

```python
import time, logging
log = logging.getLogger(__name__)

class TimingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    def __call__(self, request):
        start = time.perf_counter()
        response = self.get_response(request)
        response["Server-Timing"] = f"app;dur={(time.perf_counter() - start) * 1000:.1f}"
        return response
```

> [!warning] `DEBUG = True` in production leaks settings and stack traces. Run `manage.py check --deploy`.

## Related
- [[HTTP Headers]] · [[CORS]] (`django-cors-headers`)
- [[FastAPI Basics]] — Starlette middleware is the same idea
