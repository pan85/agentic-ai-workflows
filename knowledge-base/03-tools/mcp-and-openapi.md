---
title: MCP and OpenAPI toolsets
tags: [tools, mcp, openapi, integration]
status: draft
---

# MCP and OpenAPI toolsets

Toolsets expose many tools from one source. They let agents reuse existing
integrations without hand-writing a function per endpoint.

## Model Context Protocol (MCP)

ADK can act as an **MCP client** with `MCPToolset`, loading the tools an MCP
server advertises.

```python
from google.adk.agents import LlmAgent
from google.adk.tools.mcp_tool.mcp_toolset import MCPToolset
from google.adk.tools.mcp_tool.mcp_session_manager import StdioConnectionParams
from mcp import StdioServerParameters

files_agent = LlmAgent(
    name="files_agent",
    model="gemini-2.5-flash",
    instruction="Help the user manage files in the workspace.",
    tools=[
        MCPToolset(
            connection_params=StdioConnectionParams(
                server_params=StdioServerParameters(
                    command="npx",
                    args=["-y", "@modelcontextprotocol/server-filesystem", "/abs/path/workspace"],
                ),
            ),
            tool_filter=["read_file", "list_directory"],   # expose only what is needed
        )
    ],
)
```

- Transports: stdio (local process), SSE and streamable HTTP (remote servers),
  each with its own connection params class.
- Use `tool_filter` to limit exposure. Fewer tools means better tool selection
  and a smaller attack surface.
- ADK tools can also be **served** over MCP so other clients can use them.

> Version note: class and module names around MCP connection params have
> changed across ADK releases. Check the MCP page of the docs for the current
> import paths.

## OpenAPI

`OpenAPIToolset` turns an OpenAPI 3 spec into one tool per operation.

```python
from google.adk.tools.openapi_tool.openapi_spec_parser.openapi_toolset import OpenAPIToolset

petstore = OpenAPIToolset(spec_str=open("petstore.json").read(), spec_str_type="json")
agent = LlmAgent(name="pets", model="gemini-2.5-flash", tools=[petstore])
```

Good `operationId`s and `summary` fields in the spec become tool names and
descriptions, so improve the spec before blaming the agent.

## Security

- Treat MCP servers and API specs as code you are running: pin versions.
- Authenticated tools should use ADK's auth flow (`auth_scheme`,
  `auth_credential`) rather than keys in prompts.
- Tool results are untrusted input to the model; see
  [safety and guardrails](../11-patterns-and-practices/safety-and-guardrails.md).
