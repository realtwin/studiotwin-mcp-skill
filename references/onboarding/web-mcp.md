# Onboarding: StudioTwin Web MCP

**Status: live.** StudioTwin Web MCP can operate standalone and is also required for the Blender connector.

Public setup authority: https://docs.studiotwin.ai/docs/web-mcp

## Required connection contract

- Production endpoint: `https://api.studiotwin.ai/mcp`
- Transport: stateless Streamable HTTP, one JSON-RPC message per `POST`; no session or SSE stream. The endpoint accepts `POST` only and exposes tools only.
- Authentication: an `x-api-key` HTTP header containing the user's StudioTwin `st_...` API key.

The user must first complete [registration and API-key creation](register.md). Never place the key in chat, logs, source control, command-line arguments that will be logged, Blender, or this skill.

Before configuring the connection, check only whether `STUDIOTWIN_API_KEY`
exists in the environment that launched the current agent harness. Do not print,
log, or otherwise read its value. If it is absent, ask the user to set or provide
it securely in the harness launch environment. Do not search arbitrary local
environment or secret files without explicit authorization, and never ask the
user to paste the key into chat.

## Configure the active client

1. Add a remote Streamable HTTP MCP connection using the production endpoint.
2. Supply `x-api-key` from `STUDIOTWIN_API_KEY` through the active MCP client's
   remote-MCP secret or environment mechanism.
3. Adapt the connection to that client's supported remote-MCP configuration fields. Do not invent client-specific keys, placeholders, or interpolation syntax.
4. After adding or changing the configuration, reconnect or use the client's
   supported hot-load mechanism. If the newly configured MCP tools remain
   unavailable, ask the user to restart or reconnect the current agent harness
   session and resume the same task or conversation.

## Verify without spending credits

1. Connect to the remote MCP endpoint.
2. Run MCP initialization and tool discovery.
3. Confirm the server identifies itself as StudioTwin and exposes the current StudioTwin generation plus job/asset operations.
4. Treat the live definitions as authoritative for names, schemas, costs, and limits.

If authentication fails, check key status, organization, copied whitespace, and the exact `x-api-key` header placement.

After discovery succeeds, use the [Web MCP runtime guide](../connectors/web-mcp.md). Blender users must next complete [Blender onboarding](blender.md).