# Project instructions

## Knowledge base is part of every change

`knowledge-base/` is the project's living documentation, kept up to date
for the whole life of the project and planned as a future RAG source.

- Every change that adds or alters behaviour, structure, config, tooling or a
  decision updates the relevant knowledge base notes **in the same commit or PR**.
- New concept: add a note from `knowledge-base/00-meta/note-template.md` and
  list it in its folder's `README.md`.
- Architecture decision: add an ADR under `knowledge-base/decisions/`.
- Notes describing planned code that now exists: update them to match the real
  code and set `status: stable` once reviewed.
- Follow `knowledge-base/00-meta/conventions.md`: one concept per note, YAML
  front matter, relative links. Keep notes self-contained (clear headings, no
  "see above") so they chunk well for retrieval.
- Never put secrets or credentials in the knowledge base.
