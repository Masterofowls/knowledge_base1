---
tags: [meta, guide]
aliases: [Obsidian shortcuts, Obsidian cheat sheet]
created: 2026-10-26
---
# Obsidian Quick Reference

## Opening this vault
1. Install Obsidian (`winget install Obsidian.Obsidian` or obsidian.md).
2. **Open folder as vault** → choose `E:\knowledge_base`.
3. Trust the vault when asked; open **000 Home**. Pin it with the bookmark star.

## Keyboard shortcuts (Windows/Linux — use `Cmd` on macOS)
| Action | Shortcut |
|---|---|
| Quick switcher (open note by name/alias) | `Ctrl+O` |
| Command palette | `Ctrl+P` |
| Search all notes | `Ctrl+Shift+F` |
| New note | `Ctrl+N` |
| Toggle edit / reading view | `Ctrl+E` |
| Graph view | `Ctrl+G` |
| Insert template | `Ctrl+P` → "Templates: Insert template" |
| Navigate back / forward | `Ctrl+Alt+←` / `Ctrl+Alt+→` |
| Open link in new tab | `Ctrl+Click` |
| Fold / unfold heading | click the arrow left of a heading |
| Toggle checkbox | `Ctrl+Enter` |

## Markdown used in this vault
```markdown
[[Note Name]]                 link
[[Note Name|shown text]]      link with alias text
[[Note Name#Heading]]         link to a heading
![[Note Name]]                embed a note
#tag                          tag
- [ ] task                    checkbox (learning checklists)
> [!tip] Title                callout   (types: note, tip, info, warning, danger, example, question, success, abstract, summary)
> [!success]- Solution        collapsed callout (click to expand) — used for exercise answers
```

## Useful searches (`Ctrl+Shift+F`)
| Query | Finds |
|---|---|
| `tag:#tutorial` | all tutorials |
| `tag:#guide` | all Getting Started notes |
| `tag:#moc` | all maps of content |
| `"status: seed"` | notes you haven't practised yet |
| `path:"50 Testing" mock` | "mock" inside testing notes |
| `- [ ]` | unchecked checklist items |
| `line:(useEffect cleanup)` | both words on one line |

## Graph view tips
- Colour groups are preconfigured per topic folder (`.obsidian/graph.json`).
- Filter `tag:#moc` to see the skeleton; `-path:"70 Tutorials"` to hide tutorials.
- Local graph (right sidebar on any note) shows neighbours only — great for seeing a note's context.

## Optional community plugins
Settings → Community plugins → Browse:
- **Dataview** — e.g. a table of all seed notes:
  ````markdown
  ```dataview
  TABLE status, file.folder FROM #concept WHERE status = "seed" SORT file.folder
  ```
  ````
- **Spaced Repetition** — flashcards ([[How to Study with This Vault]]).
- **Excalidraw** — draw diagrams inside notes.
- **Obsidian Git** — automatic version history.
- **Advanced Tables** — easier table editing.

## Related
- [[Vault Guide]] · [[Note Conventions]]
