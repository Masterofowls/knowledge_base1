---
tags: [django, concept]
aliases: [FBV, CBV, class-based views, generic views]
created: 2026-10-26
status: evergreen
---
# Django Views

## Function-based views (FBV) — explicit
```python
from django.shortcuts import render, get_object_or_404, redirect
from django.contrib.auth.decorators import login_required
from django.views.decorators.http import require_http_methods

def post_detail(request, slug):
    post = get_object_or_404(Post.objects.select_related("author"), slug=slug, status="published")
    return render(request, "blog/detail.html", {"post": post})

@login_required
@require_http_methods(["GET", "POST"])
def post_create(request):
    form = PostForm(request.POST or None)
    if request.method == "POST" and form.is_valid():
        post = form.save(commit=False)
        post.author = request.user
        post.save()
        return redirect("blog:detail", slug=post.slug)     # Post/Redirect/Get
    return render(request, "blog/form.html", {"form": form})
```

## Class-based generic views (CBV) — concise
```python
from django.views.generic import ListView, DetailView, CreateView
from django.contrib.auth.mixins import LoginRequiredMixin

class PostListView(ListView):
    queryset = Post.objects.filter(status="published").select_related("author")
    paginate_by = 20
    template_name = "blog/list.html"

class PostCreateView(LoginRequiredMixin, CreateView):
    model = Post
    form_class = PostForm
    def form_valid(self, form):
        form.instance.author = self.request.user
        return super().form_valid(form)
```

> [!tip] FBVs for unusual logic; CBVs when the generic view fits ~exactly. https://ccbv.co.uk is the CBV method reference.

## JSON responses (without DRF)
```python
from django.http import JsonResponse
return JsonResponse({"id": post.id}, status=201)
```

## Related
- [[Django URLs and Routing]] · [[Django Templates]] · [[Django Forms]]
- [[Django REST Framework]] · [[HTTP Status Codes]]
