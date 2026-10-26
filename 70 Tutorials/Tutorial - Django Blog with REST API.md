---
tags: [tutorial, django, python, pytest]
aliases: [Django tutorial, blog tutorial]
created: 2026-10-26
level: intermediate
time: 4h
---
# Tutorial - Django Blog with REST API

> [!abstract] What you'll build
> A blog with posts, tags and authors: server-rendered pages, the admin, a DRF JSON API, and tests with pytest-django — including a query-count test that catches N+1 bugs.

**Prerequisites:** Python 3.12+, [[Python Classes and Dataclasses|classes]]. **Notes used:** [[Django MOC]].

## Step 1 — Project and app
```bash
uv init blogsite && cd blogsite
uv add django djangorestframework
uv add --dev pytest pytest-django
uv run django-admin startproject config .
uv run python manage.py startapp blog
```
`config/settings.py` → `INSTALLED_APPS += ["rest_framework", "blog"]`.

## Step 2 — Models
```python
# blog/models.py
from django.conf import settings
from django.db import models
from django.urls import reverse

class Tag(models.Model):
    name = models.SlugField(unique=True)
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

    class Meta:
        ordering = ["-created_at"]

    def __str__(self): return self.title
    def get_absolute_url(self): return reverse("blog:detail", kwargs={"slug": self.slug})
```
```bash
uv run python manage.py makemigrations blog
uv run python manage.py migrate
uv run python manage.py createsuperuser
```

## Step 3 — Admin
```python
# blog/admin.py
from django.contrib import admin
from .models import Post, Tag

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ["title", "author", "status", "created_at"]
    list_filter = ["status", "tags"]
    search_fields = ["title", "body"]
    prepopulated_fields = {"slug": ["title"]}
    list_select_related = ["author"]
    filter_horizontal = ["tags"]

admin.site.register(Tag)
```
> [!check] Checkpoint — `uv run python manage.py runserver`, log in at http://localhost:8000/admin, create 3 tags and 5 posts (some published).

## Step 4 — Pages: views, URLs, templates
```python
# blog/views.py
from django.shortcuts import get_object_or_404, render
from django.views.generic import ListView
from .models import Post

class PostListView(ListView):
    template_name = "blog/list.html"
    paginate_by = 10
    def get_queryset(self):
        qs = (Post.objects.filter(status=Post.Status.PUBLISHED)
              .select_related("author").prefetch_related("tags"))
        if tag := self.request.GET.get("tag"):
            qs = qs.filter(tags__name=tag)
        return qs

def post_detail(request, slug):
    post = get_object_or_404(Post.objects.select_related("author"), slug=slug, status=Post.Status.PUBLISHED)
    return render(request, "blog/detail.html", {"post": post})
```
```python
# blog/urls.py
from django.urls import path
from . import views
app_name = "blog"
urlpatterns = [
    path("", views.PostListView.as_view(), name="list"),
    path("<slug:slug>/", views.post_detail, name="detail"),
]
# config/urls.py: path("", include("blog.urls")), path("api/", include("blog.api_urls")), path("admin/", admin.site.urls)
```
Templates at `blog/templates/blog/`:
```django
{# list.html #}
<h1>Posts</h1>
{% for post in page_obj %}
  <article>
    <h2><a href="{{ post.get_absolute_url }}">{{ post.title }}</a></h2>
    <p>by {{ post.author.username }} · {% for t in post.tags.all %}<a href="?tag={{ t.name }}">#{{ t }}</a> {% endfor %}</p>
  </article>
{% empty %}<p>No posts.</p>{% endfor %}
{% if page_obj.has_next %}<a href="?page={{ page_obj.next_page_number }}">Older →</a>{% endif %}

{# detail.html #}
<h1>{{ post.title }}</h1>
<p>{{ post.created_at|date:"j M Y" }} by {{ post.author.username }}</p>
{{ post.body|linebreaks }}
<a href="{% url 'blog:list' %}">← All posts</a>
```

## Step 5 — JSON API with DRF
```python
# blog/serializers.py
from rest_framework import serializers
from .models import Post, Tag

class PostSerializer(serializers.ModelSerializer):
    author = serializers.ReadOnlyField(source="author.username")
    tags = serializers.SlugRelatedField(many=True, slug_field="name", queryset=Tag.objects.all(), required=False)
    class Meta:
        model = Post
        fields = ["id", "title", "slug", "body", "status", "author", "tags", "created_at"]
        read_only_fields = ["id", "created_at"]
```
```python
# blog/api.py
from rest_framework import permissions, viewsets
from .models import Post
from .serializers import PostSerializer

class IsAuthorOrReadOnly(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        return request.method in permissions.SAFE_METHODS or obj.author == request.user

class PostViewSet(viewsets.ModelViewSet):
    serializer_class = PostSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly, IsAuthorOrReadOnly]
    lookup_field = "slug"
    def get_queryset(self):
        return Post.objects.select_related("author").prefetch_related("tags")
    def perform_create(self, serializer):
        serializer.save(author=self.request.user)
```
```python
# blog/api_urls.py
from rest_framework.routers import DefaultRouter
from .api import PostViewSet
router = DefaultRouter()
router.register("posts", PostViewSet, basename="post")
urlpatterns = router.urls
```
> [!check] Checkpoint
> ```bash
> curl -s localhost:8000/api/posts/ | jq '.[0]'
> http -a admin:yourpassword POST :8000/api/posts/ title="From HTTPie" slug=from-httpie body="Hi" tags:='[]'
> ```
> Also open http://localhost:8000/api/ — DRF's browsable API.

## Step 6 — Tests
```toml
# pyproject.toml
[tool.pytest.ini_options]
DJANGO_SETTINGS_MODULE = "config.settings"
python_files = ["test_*.py"]
```
```python
# blog/tests/test_blog.py   (also create blog/tests/__init__.py)
import pytest
from django.urls import reverse
from blog.models import Post, Tag

@pytest.fixture
def author(django_user_model):
    return django_user_model.objects.create_user("ada", password="pw")

@pytest.fixture
def make_post(author):
    def _make(n=1, **kw):
        tag, _ = Tag.objects.get_or_create(name="django")
        posts = []
        for i in range(n):
            p = Post.objects.create(author=author, title=f"Post {i}", slug=f"post-{i}-{kw.get('status','p')}",
                                    body="...", status=kw.get("status", Post.Status.PUBLISHED))
            p.tags.add(tag)
            posts.append(p)
        return posts
    return _make

@pytest.mark.django_db
def test_list_hides_drafts(client, make_post):
    make_post(2)
    make_post(1, status=Post.Status.DRAFT)
    resp = client.get(reverse("blog:list"))
    assert resp.status_code == 200
    assert len(resp.context["page_obj"]) == 2

@pytest.mark.django_db
def test_list_query_count_is_constant(client, make_post, django_assert_max_num_queries):
    make_post(10)
    with django_assert_max_num_queries(4):          # no N+1 thanks to select/prefetch_related
        client.get(reverse("blog:list"))

@pytest.mark.django_db
def test_api_create_requires_auth(client):
    assert client.post("/api/posts/", {"title": "x"}).status_code == 403

@pytest.mark.django_db
def test_api_only_author_can_edit(client, make_post, django_user_model):
    post = make_post()[0]
    django_user_model.objects.create_user("eve", password="pw")
    client.login(username="eve", password="pw")
    resp = client.patch(f"/api/posts/{post.slug}/", {"title": "hacked"}, content_type="application/json")
    assert resp.status_code == 403
```
```bash
uv run pytest -v
```
> [!check] Checkpoint — remove `prefetch_related("tags")` from the view and watch the query-count test fail. That's an N+1 bug caught by a test ([[Django QuerySets and Performance]]).

## 🏋️ Challenges
1. Add comments (model, form on the detail page, Post/Redirect/Get) ([[Django Forms]]).
2. Add token auth with `djangorestframework-simplejwt`.
3. Generate OpenAPI docs with `drf-spectacular` and view them in Swagger UI.
4. Add full-text search with `django.contrib.postgres` (requires Postgres).
5. Write a Playwright test that publishes a post in the admin and sees it on the homepage.

## Related
- [[Tutorials MOC]] · [[Django Testing]] · [[Django REST Framework]]
