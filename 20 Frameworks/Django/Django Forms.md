---
tags: [django, snippet]
aliases: [ModelForm, form validation, CSRF]
created: 2026-10-26
status: evergreen
---
# Django Forms

```python
from django import forms

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ["title", "slug", "body", "tags"]
        widgets = {"body": forms.Textarea(attrs={"rows": 8})}

    def clean_slug(self):                         # field-level validation
        slug = self.cleaned_data["slug"]
        if slug in {"admin", "new"}:
            raise forms.ValidationError("Reserved slug")
        return slug

    def clean(self):                              # cross-field validation
        data = super().clean()
        if data.get("title", "").lower() == data.get("slug"):
            self.add_error("slug", "Slug should differ from title")
        return data
```

```django
<form method="post">
  {% csrf_token %}
  {{ form.as_div }}
  <button type="submit">Save</button>
</form>
```

Validation flow: `form.is_valid()` → `field.clean()` → `clean_<field>()` → `clean()` → `form.cleaned_data` / `form.errors`.

## Related
- [[Django Views]] · [[React Forms]]
- [[Django Settings and Middleware]] — CSRF middleware
