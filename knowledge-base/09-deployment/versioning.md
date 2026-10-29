---
title: Versioning
tags: [deployment, versioning, release]
status: draft
---

# Versioning

An agent's behaviour depends on more than code: instructions, tool schemas,
model choice and config all change outcomes. Version all of them.

## What gets a version

| Thing | How |
| --- | --- |
| Framework (`core/`) | SemVer in `pyproject.toml`, git tags `core-vX.Y.Z` |
| Each domain agent | `__version__` in the agent package, tags `<agent>-vX.Y.Z` |
| Instructions | Stored in `prompts.py` with an `INSTRUCTION_VERSION` constant |
| Model config | Role → model mapping in a config file, committed |
| Eval sets | Live next to the agent, changed in the same PR as behaviour |
| Run log schema | `schema` number in `run_start` |

## SemVer for agents

- **Major**: breaking change to the agent's contract (inputs, outputs, state
  keys other agents depend on).
- **Minor**: new capability (tool, sub-agent), or a model swap that passes evals.
- **Patch**: instruction tweaks and fixes that keep the eval scores.

## Release flow

1. Change agent code, prompts or config.
2. Update or add eval cases; run evals for the affected agent.
3. Bump the agent version and note the change in its `CHANGELOG.md`.
4. Merge; CI builds an image tagged with the version and git SHA.
5. Deploy; the run log records `agent_version` and `instruction_version` on
   every run, so any output can be traced back to the exact release.

## Rollback

Keep the previous image tag deployable. Because sessions are stored separately,
rolling back the agent does not lose conversations, but check state key
compatibility if the major version changed.
