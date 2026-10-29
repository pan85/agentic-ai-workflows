# 09 · Deployment

From laptop to production, and how agents are versioned along the way.

| Note | Summary |
| --- | --- |
| [local-development.md](local-development.md) | Python env, `.env`, `adk web` / `adk run` / `adk api_server` |
| [containers.md](containers.md) | Packaging agents as a container with `get_fast_api_app` |
| [cloud-run.md](cloud-run.md) | `adk deploy cloud_run` and manual deploys |
| [agent-engine.md](agent-engine.md) | Vertex AI Agent Engine managed runtime |
| [versioning.md](versioning.md) | Versioning agents, prompts, tools, evals and releases |

## Target comparison

| Target | Effort | Sessions | Best for |
| --- | --- | --- | --- |
| Local (`adk web`) | None | In memory or SQLite | Building and debugging |
| Container anywhere | Medium | Your database | Portability, any cloud |
| Cloud Run | Low | Your database or Vertex | Serverless HTTP API |
| Agent Engine | Low | Managed | Fully managed on Google Cloud |
| GKE | High | Your choice | Fine-grained control at scale |
