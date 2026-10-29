# 0004 · Keep project knowledge in a hierarchical Markdown knowledge base

- **Status**: Accepted
- **Date**: 2026-10-29

## Context

The project is as much about learning as building. Knowledge must stay close to
the code, be versioned with it, and be easy to browse.

## Decision

A `knowledge-base/` folder in the repo with numbered topic folders, a README
index in each, one concept per note, YAML front matter, and relative links. See
[conventions](../00-meta/conventions.md).

## Consequences

- Docs change in the same pull requests as code.
- Readable on GitHub without extra tooling; can later be published with a static
  site generator (for example MkDocs) without restructuring.
- Index READMEs must be kept up to date by hand.
