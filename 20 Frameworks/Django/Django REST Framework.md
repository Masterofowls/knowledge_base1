---
tags: [django, concept]
aliases: [DRF, serializers, viewsets]
created: 2026-10-26
status: evergreen
---
# Django REST Framework

```python
# serializers.py
from rest_framework import serializers

class PostSerializer(serializers.ModelSerializer):
    author = serializers.StringRelatedField(read_only=True)
    class Meta:
        model = Post
        fields = ["id", "title", "slug", "body", "status", "author", "created_at"]
        read_only_fields = ["id", "created_at"]

    def validate_title(self, value):
        if len(value) < 3:
            raise serializers.ValidationError("Too short")
        return value

# views.py
from rest_framework import viewsets, permissions, filters
from rest_framework.decorators import action
from rest_framework.response import Response

class PostViewSet(viewsets.ModelViewSet):
    serializer_class = PostSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly]
    filter_backends = [filters.SearchFilter, filters.OrderingFilter]
    search_fields = ["title", "body"]

    def get_queryset(self):
        return Post.objects.select_related("author")

    def perform_create(self, serializer):
        serializer.save(author=self.request.user)

    @action(detail=True, methods=["post"])
    def publish(self, request, pk=None):
        post = self.get_object(); post.status = "published"; post.save()
        return Response({"status": "published"})

# urls.py
from rest_framework.routers import DefaultRouter
router = DefaultRouter()
router.register("posts", PostViewSet, basename="post")
urlpatterns = router.urls
```

Router generates: `GET/POST /posts/`, `GET/PUT/PATCH/DELETE /posts/{pk}/`, `POST /posts/{pk}/publish/`.

> [!tip] OpenAPI docs: **drf-spectacular** → `/api/schema/` + Swagger UI. See [[OpenAPI Tooling]].

## Related
- [[REST API Design]] · [[HTTP Methods]] · [[HTTP Status Codes]]
- [[Django vs FastAPI]]
