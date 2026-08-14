# Connector: Blender MCP

**Status: in development, not yet launched.** A StudioTwin Blender addon exists
(v0.1) but is **TBD / not public** — it launches together with the remote (web)
MCP. Only the Unreal connector is active today. Do not tell a user the Blender
connector is available yet.

## Architecture (planned)

Blender does **not** re-implement platform logic. It is a **thin hands** layer on
the same backend:

- Generation goes through the **remote (web) MCP** (or REST) — see
  [web-mcp.md](web-mcp.md). Same `x-api-key` auth, same job/asset lifecycle.
- A **thin StudioTwin Blender addon** extends the community `blender-mcp`
  (ahujasid) pattern and adds StudioTwin verbs: `generate_*` plus
  **`studiotwin_import_asset(assetId)`**, which imports a generated asset into the
  Blender scene by **asset id** (via the platform's `GET /assets/:uuid`).
- This reuses the **asset-id interchange contract**: generate once on the platform,
  then import the same asset by id in Unreal *or* Blender — no duplicate generation.
- v1 scope: the natural Blender consumers — **materials, meshes, and environment
  maps**.

## For the agent, until it launches

- Route generation intent to the remote/web connector once that is live; use the
  Blender addon only for **import-by-asset-id** into the scene.
- **Discover, do not assume.** If a Blender MCP surface is configured in the host,
  connect and treat the live tool definitions as authoritative — never guess tool
  names, schemas, costs, units, or axis/orientation behavior.
- Blender-specific caveats (units, axis/orientation, import destinations, mutation
  scope) will be documented here once the addon is public.

The `SKILL.md` operating policy applies unchanged once the connector is live.

## References (internal)

- VPC-800 — StudioTwin verbs for blender-mcp (generate + import-by-asset-id). Done.
- VPC-796 — parent epic (remote server + in-engine agentic integration).
