---
tags: [django, snippet]
aliases: [DTL, template inheritance]
created: 2026-10-26
status: evergreen
---
# Django Templates

```django
{# templates/base.html #}
<!doctype html>
<html>
<head><title>{% block title %}My site{% endblock %}</title></head>
<body>
  {% include "partials/nav.html" %}
  {% if messages %}{% for m in messages %}<p class="{{ m.tags }}">{{ m }}</p>{% endfor %}{% endif %}
  {% block content %}{% endblock %}
</body>
</html>

{# templates/blog/list.html #}
{% extends "base.html" %}
{% load static %}
{% block title %}Posts{% endblock %}
{% block content %}
  {% for post in page_obj %}
    <h2><a href="{% url 'blog:detail' slug=post.slug %}">{{ post.title|title }}</a></h2>
    <p>{{ post.body|truncatewords:30 }} — {{ post.created_at|date:"Y-m-d" }}</p>
  {% empty %}
    <p>No posts yet.</p>
  {% endfor %}
  <img src="{% static 'img/logo.svg' %}" alt="logo">
{% endblock %}
```

> [!info] Auto-escaping is on by default (XSS protection). `|safe` disables it — only for trusted HTML.

> [!warning] Templates hide N+1 queries: `{{ post.author.name }}` in a loop triggers a query per row. See [[Django QuerySets and Performance]].

## Related
- [[Django Views]] · [[Django Forms]]
