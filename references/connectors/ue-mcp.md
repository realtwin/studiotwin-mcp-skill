# Connector: Unreal Engine MCP

**Status: live.** This is the primary, supported StudioTwin MCP surface today.

## What it is

StudioTwin does not ship a standalone MCP server. Its MCP surface is the
**StudioTwin Unreal Engine plugin** exposed through Epic's **Unreal MCP plugin**
(`ModelContextProtocol`, shown as "Unreal MCP" in the Plugin Browser). The Unreal
MCP server runs inside the Unreal Editor process and advertises the StudioTwin
toolkits to an MCP client (Claude Code, Cursor, VS Code, Gemini, Codex, …) over a
**local** HTTP connection (documented default `http://127.0.0.1:8000/mcp`).

Consequences that shape every session:

- **Live tool definitions are the authority.** The docs below describe the
  human-facing plugin UI; the MCP tool names, schemas, defaults, costs, and
  async behavior come from runtime discovery, not from this file. Never guess a
  tool name or schema, and never copy a credit figure into a paid call — read the
  live definition. (See the operating policy in `SKILL.md`.)
- **It is local and unauthenticated at the transport.** Do not expose the server
  for remote access. Cloud auth to StudioTwin is via the plugin's `st_` API key,
  configured in Project Settings — never in chat or MCP arguments.
- **Generation is cloud-backed and metered in credits.** Tools submit to the
  StudioTwin cloud service; most are asynchronous (submit once → poll the job id).
- **Many tools also mutate the open Unreal project** (import assets, place world
  content, load animation, create sequences). Treat those as state-changing and
  confirm scope. Downloaded intermediates land in `<ProjectRoot>/.studiotwin/`.

## Capability groups (toolkits)

Stable orientation only — discover the live tools at runtime. Each maps to a
StudioTwin toolkit visible under **Tools → STUDIOTWIN** in the Editor:

| Toolkit     | What it does (image/text → asset)                          | Docs |
| ----------- | ---------------------------------------------------------- | ---- |
| Motion      | human animation from text/trajectory; edit, stitch, retarget, load | https://docs.studiotwin.ai/docs/plugin/toolkits/motion-toolkit/ |
| Environment | HDR environment maps from text/image; upscale, outpaint; env-map → world | https://docs.studiotwin.ai/docs/plugin/toolkits/environment-toolkit/ |
| Mesh        | 3D mesh from an image reference                            | https://docs.studiotwin.ai/docs/plugin/toolkits/mesh-toolkit/ |
| Material    | PBR material from texture/image/text references           | https://docs.studiotwin.ai/docs/plugin/toolkits/material-toolkit/ |
| Audio       | sound effects from a text brief                            | https://docs.studiotwin.ai/docs/plugin/toolkits/audio-toolkit/ |

Detailed capability-selection and prompting guidance: [../capabilities.md](../capabilities.md),
[../content-guidance.md](../content-guidance.md). Job / import / mutation handling:
[../operations.md](../operations.md).

## Requirements

- Unreal Engine **5.6, 5.7, or 5.8** — each StudioTwin build is compiled against
  one specific engine version; the plugin build must match the project's engine.
- StudioTwin UE plugin installed + enabled, with a valid `st_` API key.
- Unreal MCP plugin (`ModelContextProtocol`) enabled; optional **All Toolsets**
  plugin to also expose Unreal's default toolsets. The Toolset Registry is a
  dependency of Unreal MCP and is enabled automatically.

Full install, server-start, client-config, and first-run flow: [../setup.md](../setup.md).
User onboarding (account, API key, plugin download): [../onboarding/register.md](../onboarding/register.md),
[../onboarding/plugins.md](../onboarding/plugins.md).

## Authorities

- Epic Unreal MCP: https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-mcp-in-unreal-editor
- StudioTwin plugin install: https://docs.studiotwin.ai/docs/plugin/installation/
- StudioTwin toolkits index: https://docs.studiotwin.ai/docs/plugin/toolkits/

> Credit costs per tool are published at
> https://docs.studiotwin.ai/docs/dashboard/guides/how-credits-work but change
> over time and are per-model. Use them for user-facing onboarding estimates
> only; for a paid MCP call, the **live tool definition** is authoritative.
