# Onboarding: download & install the right plugin

Which StudioTwin software a user needs depends on the connector they want to use.
Match the plugin build to the host application version.

## Unreal Engine (live connector)

Supported engine versions: **UE 5.6, 5.7, 5.8**. Each StudioTwin build is compiled
against one specific engine version — install the build that matches the project's
engine, or the modules will fail to load.

**Minimum plugin version for MCP: `3.0.0`.** The MCP surface was added in `3.0.0`;
earlier builds (e.g. `2.6.1`) still work as an in-Editor toolkit but expose **no
MCP tools**, so an MCP client discovers nothing. Before starting MCP work, confirm
the installed StudioTwin version (**Edit → Plugins → search StudioTwin →** version
shown on the card) is `3.0.0` or newer. If it is older, the user must update:

- **FAB / Epic Games Launcher:** **Fab → My Library → StudioTwin → Update** (or
  re-install into the engine version slot) to pull the latest build, then
  re-enable and restart. Updating keeps the same install method — do not mix FAB
  and manual (see below).
- **Manual:** download the current build for the exact engine version and replace
  the existing `StudioTwin` plugin folder under `Plugins/`.

After updating, re-enable StudioTwin and restart the Editor, then re-run MCP
discovery.

Two install paths (pick one and **upgrade later with the same method** — mixing
FAB and manual installs leaves two copies and breaks setup):

1. **FAB / Epic Games Launcher (recommended):**
   [StudioTwin on FAB](https://www.fab.com/listings/db820954-ce06-47de-bdc0-054b669c1727)
   → add to library → Epic Launcher → **Fab → My Library** → StudioTwin →
   **Install plugin** → pick the engine version slot → **Install**.
   Installing into the engine does **not** auto-enable it, so check the next step.
2. **Manual → project or engine:** download the build for your exact engine
   version and place the `StudioTwin` plugin folder under `Plugins/` (so that
   `StudioTwin.uplugin` sits one level below `Plugins`).

Then **enable** it: **Edit → Plugins → search StudioTwin → tick → Restart Now**.
Toolkits only appear after the editor restarts (look at the bottom of the **Tools**
menu). Configure the API key next — see [register.md](register.md).

For the MCP flow specifically, the user also needs Epic's **Unreal MCP** plugin
enabled and the MCP server started; full sequence in [../setup.md](../setup.md).

- Install guide (both paths, upgrade/uninstall, troubleshooting):
  https://docs.studiotwin.ai/docs/plugin/installation/
- Version ↔ engine matrix (changelog):
  https://docs.studiotwin.ai/docs/plugin/installation/changelog

## Blender

Not yet available — see [../connectors/blender-mcp.md](../connectors/blender-mcp.md).
Do not direct users to a Blender download until it ships.

## Web / no-DCC

No plugin download; the user needs only an account and an `st_` API key, plus the
Web MCP server configured in their client. Pending confirmation — see
[../connectors/web-mcp.md](../connectors/web-mcp.md).
