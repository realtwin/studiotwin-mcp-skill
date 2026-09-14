# Onboarding: Unreal Engine

Use this page for the StudioTwin UE plugin plus Epic's Unreal MCP. Complete [registration and API-key creation](register.md) first. Treat the [StudioTwin installation guide](https://docs.studiotwin.ai/docs/ue-plugin/installation/) and [Epic Unreal MCP guide](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-mcp-in-unreal-editor#optional:one-shotusingtheterminalplugin) as the detailed authorities.

## Ordered setup

1. **Match versions.** The native Unreal MCP path requires Unreal Engine **5.8 or newer**. StudioTwin also publishes plugin builds for Unreal Engine 5.6 and 5.7, but those versions do not provide the native Unreal MCP path described here. Install the StudioTwin build compiled for the project's exact engine version. The StudioTwin UE plugin must be **`3.0.0` or newer** for MCP; older versions such as `2.6.1` expose Editor toolkits but no MCP tools.
2. **Install StudioTwin.** Prefer [StudioTwin on Fab](https://www.fab.com/listings/db820954-ce06-47de-bdc0-054b669c1727), or install the matching manual build under a project or engine `Plugins/` directory. Keep one installation method for upgrades; mixing Fab and manual copies can leave duplicate plugins. Enable StudioTwin and restart Unreal Editor.
3. **Configure the key.** In **Edit → Project Settings → Plugins → StudioTwin**, paste the user's `st_` key into **API Key** and leave **API Endpoint URL** empty unless StudioTwin documentation or support explicitly directs otherwise. Confirm Unreal accepts it; never ask the user to reveal it.
4. **Enable Unreal MCP.** In **Edit → Plugins**, enable **Unreal MCP** (`ModelContextProtocol`). Enable optional **All Toolsets** only when Unreal's default toolsets are also needed, then restart. Unreal MCP enables the Toolset Registry dependency automatically.
5. **Start the local server.** In the Unreal console, run `ModelContextProtocol.StartServer` or `ModelContextProtocol.StartServer 8000`. The documented default is `http://127.0.0.1:8000/mcp`; it is unauthenticated at the transport and must remain local.
6. **Generate client configuration.** Run `ModelContextProtocol.GenerateClientConfig <client>` from the Unreal console using Epic's documented value for the active client: `ClaudeCode`, `Cursor`, `VSCode`, `Gemini`, `Codex`, or `All`. Launch the client from the project or workspace root where Unreal wrote the configuration.
7. **Discover.** Connect, list the live MCP tools, and confirm StudioTwin capability groups are present. Do not spend credits merely to test setup.

If discovery fails, confirm the intended project is open, both plugins are enabled, StudioTwin is `3.0.0+`, the server started, the client uses the generated root and endpoint, and Unreal accepted the API key. Retry only after the relevant host-side correction.

Version matrix and upgrade details: https://docs.studiotwin.ai/docs/ue-plugin/installation/changelog

After discovery succeeds, use the [Unreal MCP runtime guide](../connectors/ue-mcp.md).