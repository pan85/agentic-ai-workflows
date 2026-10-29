---
title: Run logs
tags: [observability, logging, schema]
status: draft
---

# Run logs

A **run log** is the project's structured, persisted record of one invocation.
It complements the session (which ADK owns) with data ADK does not keep:
timings, model ids, versions and records from nested agents.

## Format

JSON Lines, one record per line, written by the `RunLogPlugin`. Every record
shares a header:

```json
{"ts": "2026-10-29T12:00:00.123Z", "invocation_id": "e-7f3c", "session_id": "s-91",
 "user_id": "u1", "app": "help_desk", "trace_id": "4bf92f...", "type": "..."}
```

## Record types

| `type` | Extra fields |
| --- | --- |
| `run_start` | `agent_version`, `instruction_version`, `models` (role → id), `user_message` |
| `agent_start` / `agent_end` | `agent`, `parent`, `duration_ms` |
| `model_call` | `agent`, `model`, `input_tokens`, `output_tokens`, `duration_ms`, `finish_reason` |
| `tool_call` | `agent`, `tool`, `call_id`, `args`, `result` (or `artifact_ref`), `status`, `duration_ms` |
| `state_change` | `agent`, `delta` |
| `transfer` | `from`, `to`, `reason` |
| `guardrail` | `rule`, `action` (blocked, redacted), `detail` |
| `error` | `where`, `message` |
| `run_end` | `final_response`, `duration_ms`, `total_tokens`, `cost_estimate` |

## Rules

- Redact secrets and personal data before writing (`args` and `result` pass
  through a redaction function).
- Truncate large values to a preview and store the full value as an artifact.
- Write locally to `runs/<date>/<invocation_id>.jsonl` in development; ship to a
  log backend (for example Cloud Logging) in production.
- The schema is versioned (`"schema": 1` in `run_start`); bump it on breaking changes.

## Uses

- Rendering [reasoning paths](reasoning-paths.md).
- Building eval cases from real runs.
- Cost and latency dashboards per agent and model.
- Comparing providers on the same inputs.
