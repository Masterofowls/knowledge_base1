---
tags: [django, snippet]
aliases: [makemigrations, migrate, data migration]
created: 2026-10-26
status: evergreen
---
# Django Migrations

```bash
python manage.py makemigrations            # detect model changes → migration file
python manage.py makemigrations blog --name add_slug
python manage.py migrate                   # apply
python manage.py migrate blog 0003         # roll back to 0003
python manage.py showmigrations
python manage.py sqlmigrate blog 0004      # see the SQL
python manage.py makemigrations --check --dry-run   # CI: fail if missing migrations
```

## Data migration
```python
from django.db import migrations

def fill_slugs(apps, schema_editor):
    Post = apps.get_model("blog", "Post")      # historical model, NOT a direct import
    for p in Post.objects.filter(slug=""):
        p.slug = slugify(p.title); p.save(update_fields=["slug"])

class Migration(migrations.Migration):
    dependencies = [("blog", "0004_post_slug")]
    operations = [migrations.RunPython(fill_slugs, migrations.RunPython.noop)]
```

> [!warning] Zero-downtime changes
> Adding a NOT NULL column to a large table: (1) add nullable, (2) backfill, (3) add constraint. Renaming columns breaks the old code still running during deploy — use expand/contract.

## Related
- [[Django Models and ORM]]
- [[SQL DDL and Constraints]]
