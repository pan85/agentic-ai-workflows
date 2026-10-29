---
title: Artifacts
tags: [artifacts, files]
status: stable
---

# Artifacts

Artifacts are named, **versioned** binary or text blobs (PDFs, images, CSVs,
generated reports) stored outside the conversation. Each save creates a new
version.

## Services

| Service | Storage |
| --- | --- |
| `InMemoryArtifactService` | Process memory |
| `GcsArtifactService` | Google Cloud Storage bucket |

Pass one to the Runner: `Runner(..., artifact_service=GcsArtifactService(bucket_name="..."))`.

## Saving and loading

```python
from google.genai import types
from google.adk.tools import ToolContext

async def export_report(tool_context: ToolContext) -> dict:
    """Generate the monthly report as CSV and store it."""
    csv_bytes = build_report_csv(tool_context.state["report:rows"])
    part = types.Part.from_bytes(data=csv_bytes, mime_type="text/csv")
    version = await tool_context.save_artifact(filename="monthly_report.csv", artifact=part)
    return {"status": "success", "artifact": "monthly_report.csv", "version": version}
```

- `load_artifact(filename, version=None)` returns the latest version by default.
- Prefix the filename with `user:` (for example `user:profile.png`) to share it
  across the user's sessions.
- The `load_artifacts` tool lets the model pull artifacts into context.

## Why artifacts matter for transparency

Large tool outputs belong in artifacts, not state or tool results. Events then
carry an `artifact_delta` (filename and version) instead of the payload, keeping
traces readable while still pointing at the exact data the agent used.
