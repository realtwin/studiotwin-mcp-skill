# StudioTwin MCP setup

Use this page only to choose and follow the correct setup path. Keep setup user-led: the agent explains the steps, attempts connection and discovery, and reports what remains unresolved. Never ask the user to paste an API key into chat.

## Ordered setup checklist

Choose one connector and complete every linked page in order:

### Web / no DCC

1. [Create the StudioTwin account and API key](onboarding/account-api-key.md).
2. [Connect hosted StudioTwin Web MCP](onboarding/web-mcp.md).
3. After connection succeeds, use the [Web MCP runtime guide](connectors/web-mcp.md).

Setup is complete when the active client discovers the live StudioTwin tools without a paid generation.

### Blender

1. [Create the StudioTwin account and API key](onboarding/account-api-key.md).
2. [Connect hosted StudioTwin Web MCP](onboarding/web-mcp.md).
3. [Install and verify official Blender MCP and the StudioTwin Blender addon](onboarding/blender.md).
4. After both MCP connections and the addon are verified, use the [Blender MCP runtime guide](connectors/blender-mcp.md).

Blender depends on Web MCP for StudioTwin cloud activity. Web MCP can also operate standalone.

### Unreal Engine

1. [Create the StudioTwin account and API key](onboarding/account-api-key.md).
2. [Install and connect the StudioTwin UE plugin and Unreal MCP](onboarding/unreal.md).
3. After connection succeeds, use the [Unreal MCP runtime guide](connectors/ue-mcp.md).

Setup is complete when the active client discovers the live StudioTwin capability groups without a paid generation.

## Shared follow-up

- Credits and cost expectations: [onboarding/credits.md](onboarding/credits.md)
- Connection failures: [troubleshooting.md](troubleshooting.md)

Do not skip ahead to a runtime guide when setup is incomplete. Do not invent endpoints, credentials, tool names, or client configuration syntax.