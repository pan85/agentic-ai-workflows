---
title: Knowledge base conventions
tags: [meta]
status: stable
---

# Knowledge base conventions

## Structure

- **Top-level folders are topics**, prefixed with a two-digit number (`03-tools`)
  that reflects learning order. Numbers are stable; add new topics at the end.
- **Every folder has a `README.md`** that lists its notes in a table with a
  one-line summary. Update it whenever you add, rename or delete a note.
- **One concept per note.** If a note grows past roughly 300 lines or covers two
  ideas, split it and link the halves.
- Go at most **two levels deep** (`topic/subtopic/note.md`). Deeper trees are
  hard to browse on GitHub.

## File names

- `kebab-case.md`, lowercase, no dates, no spaces.
- Name the concept, not the action: `state.md`, not `how-to-use-state.md`.
- ADRs live in `decisions/` as `NNNN-short-title.md`.

## Front matter

Each note starts with YAML front matter:

```yaml
---
title: Human readable title
tags: [adk, tools]          # free-form, lowercase
status: draft | stable | outdated
---
```

- `draft`: being written, may be incomplete or wrong.
- `stable`: reviewed and believed correct for the ADK version we use.
- `outdated`: kept for history; the note must link to its replacement.

## Writing style

- Lead with a short summary of what the concept is and why it matters here.
- Prefer small, runnable Python snippets over prose. Import paths matter: show them.
- Call out version-sensitive details with a `> Version note:` quote block.
- End with **Transparency notes** (what this concept exposes or hides at run
  time) when relevant, and **See also** links.

## Linking

- Use relative links: `[state](../04-sessions-state-memory/state.md)`.
- Link the first mention of a concept that has its own note.
- Terms with a precise meaning belong in the [glossary](glossary.md).

## Keeping it current

The knowledge base is maintained through the whole project life cycle, not
written once. Every change to the project updates the affected notes in the
same commit or PR (see the repository `CLAUDE.md`). Because the notes are a
future RAG source, keep each note self-contained, with descriptive headings
and accurate front matter.

## Review

- When ADK is upgraded, search for `Version note` and re-check those notes.
- Decisions that change the architecture get an ADR, and the affected notes link to it.
