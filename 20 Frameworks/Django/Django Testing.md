---
tags: [django, testing, snippet]
aliases: [pytest-django, Django TestCase]
created: 2026-10-26
status: evergreen
---
# Django Testing

## With pytest-django (recommended)
```toml
# pyproject.toml
[tool.pytest.ini_options]
DJANGO_SETTINGS_MODULE = "config.settings.test"
python_files = ["test_*.py"]
```

```python
import pytest
from django.urls import reverse

@pytest.fixture
def post(db, django_user_model):                # `db` fixture enables DB access
    user = django_user_model.objects.create_user("ada", password="pw")
    return Post.objects.create(author=user, title="Hello", slug="hello", body="...", status="published")

def test_detail_page(client, post):
    resp = client.get(reverse("blog:detail", kwargs={"slug": post.slug}))
    assert resp.status_code == 200
    assert "Hello" in resp.content.decode()

def test_create_requires_login(client):
    resp = client.post(reverse("blog:create"), {"title": "x"})
    assert resp.status_code == 302 and "/login" in resp.url

def test_api_create(admin_client):
    resp = admin_client.post("/api/posts/", {"title": "New", "slug": "new", "body": "b"},
                             content_type="application/json")
    assert resp.status_code == 201
```

Useful fixtures: `client`, `admin_client`, `rf` (RequestFactory), `db`, `transactional_db`, `django_user_model`, `settings`, `django_assert_num_queries`, `mailoutbox`.

Test data factories: **factory_boy** / **model_bakery**.

## Built-in unittest style
```python
from django.test import TestCase
class PostTests(TestCase):
    @classmethod
    def setUpTestData(cls): cls.user = User.objects.create_user("ada")
    def test_str(self): self.assertEqual(str(Post(title="x")), "x")
```

## Related
- [[pytest Fixtures]] · [[pytest MOC]]
- [[Django QuerySets and Performance]] — asserting query counts
- [[Testing Pyramid]]
