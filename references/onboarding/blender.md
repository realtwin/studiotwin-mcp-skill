# Onboarding: Blender

**Status: released.** The workflow depends on StudioTwin Web MCP for all cloud generation, uploads, jobs, downloads, and asset activity; Web MCP can also operate standalone.

Public setup authority: https://docs.studiotwin.ai/docs/blender-addon/mcp-setup

Complete [account and API-key creation](account-api-key.md), then [connect and verify Web MCP](web-mcp.md). Do not duplicate or improvise its endpoint, authentication, or client configuration here.

## Ordered setup

1. **Install Blender 5.1 or newer.** Keep the intended `.blend` open for verification.
2. **Install official Blender MCP.** Follow Blender's [MCP Server guide](https://www.blender.org/lab/mcp-server/). Install and enable its add-on, enable **Allow Online Access**, and confirm its preferences report **Server is running**. Keep the bridge on loopback (`localhost`; documented default port `9876`) and never expose it to a network.
3. **Connect the official MCP server.** Use the active client's supported route: the latest `.mcpb` from the official [releases](https://projects.blender.org/lab/blender_mcp/releases), or the official [stdio source setup](https://projects.blender.org/lab/blender_mcp/wiki/Setup) using `uv --directory <clone-path>/mcp run blender-mcp`. When defaults change, keep `BLENDER_MCP_HOST` and `BLENDER_MCP_PORT` consistent with the Blender bridge.
4. **Install the StudioTwin Blender addon.** Download the current `.zip` from the [StudioTwin Blender addon releases page](https://github.com/realtwin/studiotwin-blender-addon/releases). In **Preferences → Get Extensions**, choose **Install from Disk**, select the `.zip`, enable **StudioTwin**, and grant its declared local-file permission. The addon is not an MCP server, makes no network requests, and stores no API key.
5. **Verify official Blender MCP.** Call a read-only summary tool, then use `execute_blender_code` to return `bpy.app.background` and the Blender version. The intended open scene must report `background: false`.
6. **Verify the addon.** Through a read-only code call, discover the enabled addon module named `studiotwin` or ending with `.studiotwin`, import it, and confirm `apply_environment_map`, `import_model`, `import_material`, and `import_audio` are callable.

Official Blender MCP can execute LLM-generated Python without guards. Blender recommends a virtual machine or system without sensitive data. Do not use an untrusted scene, expose the bridge, or interpolate untrusted text into executable code.

Setup is complete only when Web MCP and official Blender MCP both connect and Blender reports all four importer functions. Do not run a paid generation merely to test setup. Installing the StudioTwin Blender addon does not install official Blender MCP.

StudioTwin Blender motion import is **in progress**. Do not attempt or improvise it until the live addon exposes it.

After verification, use the [Blender MCP runtime guide](../connectors/blender-mcp.md).