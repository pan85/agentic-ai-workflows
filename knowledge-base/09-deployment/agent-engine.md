---
title: Vertex AI Agent Engine
tags: [deployment, gcp, agent-engine, managed]
status: draft
---

# Vertex AI Agent Engine

Agent Engine is Google Cloud's managed runtime for agents. It hosts the agent,
manages sessions and memory, and integrates with Cloud Trace and Logging.

## Deploy

```bash
adk deploy agent_engine \
  --project "$GOOGLE_CLOUD_PROJECT" \
  --region us-central1 \
  --staging_bucket gs://my-staging-bucket \
  agents/billing_agent
```

Or from Python with the Vertex AI SDK (`vertexai.agent_engines.create(...)`)
wrapping the agent in an `AdkApp`.

> Version note: CLI flags and SDK entry points have changed several times; check
> the current deploy docs before scripting this.

## What you get

- Managed sessions (`VertexAiSessionService`) and Memory Bank
  (`VertexAiMemoryBankService`).
- Scaling and hosting without managing containers.
- Tracing to Cloud Trace.

## Trade-offs

| Pro | Con |
| --- | --- |
| Least operational work | Tied to Google Cloud |
| Built-in sessions and memory | Less control over runtime and networking |
| Native Gemini and Vertex Claude access | Non-Google providers need keys via secrets |

For a model-agnostic, portable setup, keep containers as the reference target
and treat Agent Engine as one option.
