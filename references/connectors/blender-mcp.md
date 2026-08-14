# Connector: Blender MCP

**Status: planned / not yet available.** _(Placeholder — 2026-08-14. Awaiting
StudioTwin material.)_

This reference is a stub for a future StudioTwin Blender MCP surface. Until it
ships and this file is completed:

- **Do not claim a Blender connector exists or is supported.** If a user asks for
  StudioTwin-in-Blender, tell them it is not yet available and point them to the
  live Unreal Engine connector ([ue-mcp.md](ue-mcp.md)) or the web connector
  ([web-mcp.md](web-mcp.md)) if that is a fit.
- **Discover, do not assume.** If a Blender MCP surface is configured in the host,
  connect and treat the live tool definitions as the authority — exactly as with
  UE. Never guess tool names, schemas, costs, or import behavior.

## To be filled in when material lands

- How StudioTwin exposes MCP inside Blender (add-on? external server? transport).
- Install + connect flow (Blender version support, add-on install, API key config).
- Capability groups available in Blender vs. UE (parity or subset).
- Where downloaded intermediates land and how assets import into the `.blend`.
- Blender-specific paths, units, axis/orientation, and mutation-scope caveats.
- Authoritative docs link.

The operating policy in `SKILL.md` (plan from intent, inspect before acting,
submit-once/poll, verify results, report concisely, never expose secrets) applies
unchanged to any Blender connector once live.
