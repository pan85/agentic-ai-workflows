---
title: Containers
tags: [deployment, docker]
status: draft
---

# Containers

Packaging agents as a container makes them deployable on any platform.

## FastAPI entry point

ADK exposes the same app `adk api_server` uses:

```python
# main.py
import os
import uvicorn
from google.adk.cli.fast_api import get_fast_api_app

app = get_fast_api_app(
    agents_dir=os.path.join(os.path.dirname(__file__), "agents"),
    session_service_uri=os.environ.get("SESSION_DB_URL", "sqlite+aiosqlite:///./sessions.db"),
    allow_origins=["*"],
    web=False,                      # True also serves the dev UI
)

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=int(os.environ.get("PORT", 8080)))
```

> Version note: `get_fast_api_app` parameters change between releases; check
> the deploy docs or the function signature.

## Dockerfile sketch

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY pyproject.toml ./
RUN pip install --no-cache-dir .
COPY core/ core/
COPY agents/ agents/
COPY main.py .
RUN useradd -m app && chown -R app /app
USER app
ENV PORT=8080
CMD ["python", "main.py"]
```

## Practices

- Inject keys as runtime secrets, never bake them into the image.
- Use a real database (PostgreSQL with an async driver) for sessions.
- Ship only `core/` and `agents/` into the production image; keep tests and
  docs out.
- Health check: `GET /list-apps` is a cheap readiness probe.
- Tag images with the git SHA and the release version (see [versioning](versioning.md)).
