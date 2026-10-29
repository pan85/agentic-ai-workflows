---
title: Gemini
tags: [models, gemini, google]
status: draft
---

# Gemini

Gemini is ADK's native model family. It needs no extra package.

## Access paths

| Path | Environment |
| --- | --- |
| Google AI Studio (API key) | `GOOGLE_GENAI_USE_VERTEXAI=FALSE`, `GOOGLE_API_KEY=...` |
| Vertex AI (GCP project) | `GOOGLE_GENAI_USE_VERTEXAI=TRUE`, `GOOGLE_CLOUD_PROJECT`, `GOOGLE_CLOUD_LOCATION`, plus `gcloud auth application-default login` |

## Typical model choices

- **Flash**: default for routing, tool use and most agents (fast, cheap).
- **Flash-Lite**: high-volume, simple tasks.
- **Pro**: hard reasoning, long documents, planning.

Check the current model list; ids change with each release.

## Native-only features

- Built-in tools: Google Search grounding, code execution, Vertex AI Search, URL context.
- `BuiltInPlanner` with `ThinkingConfig` (thinking budget, `include_thoughts`).
- Live bidirectional audio/video streaming (`run_live`).
- Very long context windows.

## Tips

- Set `generate_content_config=types.GenerateContentConfig(temperature=0.2)` for
  deterministic tool-using agents.
- With `include_thoughts=True`, thought parts arrive in events with
  `part.thought == True`. Render them separately from the answer.
