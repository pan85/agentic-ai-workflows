---
title: Tracing
tags: [observability, opentelemetry, tracing]
status: draft
---

# Tracing

ADK is instrumented with **OpenTelemetry**. Spans give timing and nesting that
the event list alone does not.

## Span hierarchy

```text
invocation
└── agent_run [help_desk]
    ├── call_llm            (model, request/response, tokens)
    └── agent_run [billing] (after transfer)
        ├── call_llm
        ├── execute_tool [search_orders]
        └── call_llm
```

> Version note: span names and attributes follow ADK's tracing module and the
> OpenTelemetry GenAI semantic conventions, both of which are evolving.

## Exporting traces

**Google Cloud Trace**

```bash
adk web --trace_to_cloud agents/
adk deploy cloud_run --trace_to_cloud ...
```

**Any OTLP backend** (Jaeger, Grafana Tempo, Arize Phoenix, Langfuse, ...):
configure a tracer provider before creating the Runner.

```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter

provider = TracerProvider()
provider.add_span_processor(BatchSpanProcessor(OTLPSpanExporter(endpoint="http://localhost:4318/v1/traces")))
trace.set_tracer_provider(provider)
```

Several observability vendors (Arize/Phoenix, AgentOps, Langfuse, W&B Weave)
provide ADK-specific integrations that add richer LLM views.

## What to look at

- **Latency**: which span dominates (usually model calls).
- **Token usage** per `call_llm`.
- **Tool errors** and retries.
- **Unexpected nesting**: an agent calling a tool you did not expect.

## Local default for this project

Run a local OTLP collector with a UI (for example Jaeger or Phoenix) during
development, and Cloud Trace in deployed environments. Keep sampling at 100% in
dev and tune it in production.

## Linking traces to run logs

Store the trace id in the [run log](run-logs.md) header so you can jump from a
readable reasoning path to the raw timing view.
