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

## Blender (in development)

The StudioTwin Blender extension and hosted StudioTwin MCP are not public yet.
Use these instructions only when the operator has been given the StudioTwin
extension package and hosted MCP connection details. Otherwise explain that the
connector has not launched and do not invent a download or production endpoint.

The Blender workflow requires three separately installed components:

1. **Blender 5.1 or newer.**
2. **Official Blender MCP:** Blender Lab's local bridge into the open Blender
   process, consisting of a Blender extension and an MCP server launched by the
   agent's MCP client.
3. **StudioTwin Blender extension:** local importer functions only. It reads
   StudioTwin outputs from disk; it performs no network requests and stores no
   API key.

The agent also needs the hosted StudioTwin MCP configured separately for cloud
generation, jobs, uploads, and asset resolution.

### 1. Configure StudioTwin access — User

Create the account and `st_` API key as described in [register.md](register.md).
Configure the key in the hosted StudioTwin MCP entry supplied by StudioTwin,
using the MCP client's secret or environment configuration. Never paste the key
into chat, Blender, the StudioTwin extension, source control, or command-line
arguments that will be logged.

Because the hosted MCP is not public yet, the operator must use the endpoint and
configuration supplied with their approved build. Leave this stage unresolved
if they have not received those details.

### 2. Install official Blender MCP — User

1. Install and launch Blender 5.1 or newer.
2. In Blender Preferences, add the Blender Lab extension repository using
   `https://lab.blender.org/`.
3. Open **Get Extensions**, find **MCP**, install it, and enable it.
4. Ensure Blender's **Allow Online Access** preference is enabled. The MCP
   extension uses a local TCP socket but refuses to start while Blender online
   access is disabled.
5. Open the MCP extension preferences. Keep the documented defaults unless the
   client configuration requires otherwise: host `localhost`, port `9876`.
   **Auto Start** is enabled by default; otherwise click
   **Start MCP Bridge Server** and confirm the UI reports
   **Server is running**.
6. Install the official MCP server package in the environment used by the MCP
   client:

   ```text
   pip install git+https://projects.blender.org/lab/blender_mcp.git#subdirectory=mcp
   ```

7. Configure the MCP client to launch `blender-mcp`. If host or port were
   changed, set `BLENDER_MCP_HOST` and `BLENDER_MCP_PORT` consistently in that
   MCP server configuration.

Official project and current installation guidance:

- https://projects.blender.org/lab/blender_mcp
- https://www.blender.org/lab/mcp-server/

### 3. Install the StudioTwin Blender extension — User

1. Obtain the current StudioTwin Blender extension `.zip` from the approved
   StudioTwin distribution channel.
2. In Blender, open **Preferences → Get Extensions**, use the menu to choose
   **Install from Disk**, select the `.zip`, and enable **StudioTwin**.
3. Grant its declared local-file permission when Blender requests it. The
   extension does not require network permission or an API key.

Blender extension manifests cannot declare another separately installed
extension as a dependency, so installing StudioTwin does not install or enable
official Blender MCP automatically.

### 4. Verify each layer — Shared

Verify without spending credits:

1. **Agent:** discover the hosted StudioTwin MCP tools and confirm the live
   generation and job/asset capabilities are present.
2. **User:** keep the intended Blender file open and confirm the official MCP
   preferences say **Server is running**.
3. **Agent:** call a read-only official Blender MCP summary tool, then use
   `execute_blender_code` to return `bpy.app.background` and the Blender version.
   The intended open-scene connection must report `background: false`.
4. **Agent:** through the same read-only code call, discover the enabled module
   whose name is `studiotwin` or ends with `.studiotwin`, import it, and confirm
   that `apply_environment_map`, `import_model`, `import_material`, and
   `import_audio` are callable.

Setup is complete only when both MCP connections work and Blender reports the
four importer functions. Do not run a paid generation merely to test setup.
Operating workflow and importer behavior:
[../connectors/blender-mcp.md](../connectors/blender-mcp.md).

## Web / no-DCC

No plugin download; the user needs only an account and an `st_` API key, plus the
Web MCP server configured in their client. Pending confirmation — see
[../connectors/web-mcp.md](../connectors/web-mcp.md).
