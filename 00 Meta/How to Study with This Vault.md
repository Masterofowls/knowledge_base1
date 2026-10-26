---
tags: [meta, guide]
aliases: [Study guide, Learning method]
created: 2026-10-26
---
# How to Study with This Vault

> [!summary] The loop
> **Read** a note → **Run** its code → **Build** in a tutorial → **Write** what you learned in your own words → **Review** it later.

## 1. Pick a track
Each topic MOC starts with a 🚀 *Getting Started* note containing install steps, a hello-world, and a **learning checklist**. Pick a track from [[Tutorials MOC]] or [[000 Home]] and follow its order.

## 2. Weekly rhythm (≈ 6–8 h/week)
| Day | Activity | Time |
|---|---|---|
| Mon | Read 2–3 concept notes; run every code block | 1 h |
| Tue | Tutorial steps | 1.5 h |
| Wed | Read + small exercises; tick checklist items | 1 h |
| Thu | Tutorial steps / challenges | 1.5 h |
| Fri | Review: flashcards + rewrite one note in your own words | 45 min |
| Weekend | Free project: combine 2 topics | 1–2 h |

## 3. Active reading of a note
1. Read the **TL;DR** callout. Predict how the code will behave.
2. Run the code. Change one thing. Break it on purpose — read the error.
3. Follow at least **one link** to a related note in another topic.
4. Add your own example under a `## My notes` heading.
5. Update frontmatter `status:` — `seed` (read) → `growing` (practised) → `evergreen` (could teach it).

## 4. Daily note workflow
Open today's note (`Ctrl+P` → "Open today's daily note"), then log:
```markdown
## 📚 Learned today
- `prefetch_related` does a second query with IN (...) — [[Django QuerySets and Performance]]
## ❓ Questions to research
- How does Postgres choose between index scan and seq scan?
```
Later, turn questions into notes with the **Question Note** template; link them from the topic MOC.

## 5. Spaced repetition (optional)
Install the community plugin **Spaced Repetition**, tag notes `#flashcards`, and write cards inline:
```markdown
What does 422 mean in FastAPI?::Request was well-formed JSON but failed validation.
Difference between `useMemo` and `useCallback`?::useMemo caches a value; useCallback caches a function.
```
Review 10 minutes a day.

## 6. Feynman check
For every MOC, try to explain the **Fundamentals** section out loud (or in a note) without looking. Where you hesitate → reread that note.

## 7. Project ideas combining topics
- Bookmarks app: [[Express MOC|Express]] or [[FastAPI MOC|FastAPI]] API + [[React MOC|React]]/[[Redux MOC|Redux]] UI + [[Playwright MOC|Playwright]] E2E + [[OpenAPI MOC|OpenAPI]] contract.
- Log analytics: [[Bash MOC|Bash]]/[[PowerShell MOC|PowerShell]] collector → [[SQL MOC|SQL]] database → [[Django MOC|Django]] admin dashboard.
- API monitor: [[curl MOC|curl]] smoke tests in CI + [[pytest MOC|pytest]] contract tests.

## 8. Keeping the vault healthy
- Weekly: empty `Inbox/`, link orphans (Graph view → toggle *Orphans*), update MOCs.
- Monthly: review notes still marked `seed`.
- Prefer **editing** existing notes over creating near-duplicates.

## Related
- [[Vault Guide]] · [[Obsidian Quick Reference]] · [[Development Environment Setup]]
