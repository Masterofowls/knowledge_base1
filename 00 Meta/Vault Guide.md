---
tags: [meta]
created: 2026-10-26
---
# Vault Guide

## Structure
| Folder | Purpose |
|---|---|
| `000 Home.md` | Entry point, links every MOC |
| `00 Meta/` | How the vault works |
| `05 Concepts/` | Cross-cutting ideas that span technologies |
| `10 Languages/` | JavaScript, TypeScript, Python |
| `20 Frameworks/` | React, Redux, Express, Django, FastAPI |
| `30 Data/` | SQL |
| `40 Shell/` | Bash, PowerShell |
| `50 Testing/` | Fundamentals, pytest, Jest, Playwright, Supertest |
| `60 HTTP & APIs/` | HTTP, curl, HTTPie, OpenAPI |
| `70 Tutorials/` | Step-by-step projects with checkpoints & challenges |
| `Inbox/` | Unprocessed captures (default new-note location) |
| `Daily/` | Daily learning logs |
| `Templates/` | Note templates (core Templates plugin) |

## Workflow (CODE: Capture → Organize → Distill → Express)
1. **Capture** — dump into `Inbox/` or today's daily note. Don't organize yet.
2. **Organize** — weekly: rename, tag, move into a topic folder, link from the MOC.
3. **Distill** — rewrite in your own words; add a `> [!summary]` TL;DR; bold the key line.
4. **Express** — use notes to write code, blog posts, interview answers. Link back.

## Principles
- **Atomic notes**: one concept per note; titles are unique and descriptive.
- **Link generously**: a note without links is a lost note. Prefer linking to concepts over copying.
- **MOCs over folders**: folders are coarse; MOCs encode meaning and order.
- **Status lifecycle**: `seed` → `growing` → `evergreen` (frontmatter `status`).

## Recommended community plugins (optional)
- **Dataview** — query notes by tag/status (e.g. list all `status: seed`).
- **Templater** — richer templates.
- **Spaced Repetition** — turn notes into flashcards (`question::answer`).
- **Excalidraw** — diagrams inside the vault.

## First time here?
1. Set up your tools → [[Development Environment Setup]]
2. Learn the Obsidian basics → [[Obsidian Quick Reference]]
3. Pick a track and a routine → [[How to Study with This Vault]]
4. Open a topic MOC and start with its 🚀 Getting Started note.

See also [[Note Conventions]], [[Tag Index]].
