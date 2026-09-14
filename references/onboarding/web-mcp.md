# Onboarding: StudioTwin Web MCP

**Status: live.** StudioTwin Web MCP can operate standalone and is also required for the Blender connector.

Public setup authority: https://docs.studiotwin.ai/docs/web-mcp

## Required connection contract

- Production endpoint: `https://api.studiotwin.ai/mcp`
- Transport: stateless Streamable HTTP, one JSON-RPC message per `POST`; no session or SSE stream. The endpoint accepts `POST` only and exposes tools only.
- Authentication: an `x-api-key` HTTP header containing the user's StudioTwin `st_...` API key.

The user must first complete [registration and API-key creation](register.md). Never place the key in chat, logs, source control, command-line arguments that will be logged, Blender, or this skill.

## Configure the active client

1. Add a remote Streamable HTTP MCP connection using the production endpoint.
2. Supply `x-api-key` through the active client's remote-MCP secret or environment mechanism.
3. Adapt the connection to that client's supported remote-MCP configuration fields. Do not invent client-specific keys, placeholders, or interpolation syntax.
4. Restart or reconnect the client after the configuration changes.

## Verify without spending credits

1. Connect to the remote MCP endpoint.
2. Run MCP initialization and tool discovery.
3. Confirm the server identifies itself as StudioTwin and exposes the current StudioTwin generation plus job/asset operations.
4. Treat the live definitions as authoritative for names, schemas, costs, and limits.

If authentication fails, check key status, organization, copied whitespace, and the exact `x-api-key` header placement.

After discovery succeeds, use the [Web MCP runtime guide](../connectors/web-mcp.md). Blender users must next complete [Blender onboarding](blender.md).