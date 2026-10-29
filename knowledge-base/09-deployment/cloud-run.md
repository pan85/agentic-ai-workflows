---
title: Cloud Run
tags: [deployment, gcp, cloud-run]
status: draft
---

# Cloud Run

Serverless containers on Google Cloud. Good default for an HTTP agent API.

## One-command deploy

```bash
adk deploy cloud_run \
  --project "$GOOGLE_CLOUD_PROJECT" \
  --region europe-west1 \
  --service_name billing-agent \
  --with_ui \
  --trace_to_cloud \
  agents/billing_agent
```

ADK builds a container and deploys it. `--with_ui` also serves the dev UI,
which is fine for internal testing only.

## Manual deploy

Build your own image (see [containers](containers.md)) and run:

```bash
gcloud run deploy billing-agent \
  --image europe-west1-docker.pkg.dev/$PROJECT/agents/billing-agent:1.2.0 \
  --region europe-west1 \
  --set-secrets ANTHROPIC_API_KEY=anthropic-key:latest \
  --set-env-vars SESSION_DB_URL=postgresql+asyncpg://... \
  --no-allow-unauthenticated
```

## Considerations

- Requests can run long; raise the request timeout for multi-step agents.
- Cold starts: set a minimum instance count for latency-sensitive agents.
- Use Cloud SQL or Vertex AI sessions; in-memory sessions are lost between instances.
- Put the service behind authentication (IAM or an API gateway).
- Model keys from Secret Manager; Vertex AI models use the service account.
