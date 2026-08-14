# Connector: Remote (Web) MCP

**Status: built, not yet public.** The remote MCP server exists (StudioTwin API,
VPC-797) but is in **internal review** and launches **alongside the Blender
connector** — it is *not live for end users today*. Only the Unreal connector is
active right now. Do not tell a user the remote connector is available yet, and do
not advertise a production endpoint until StudioTwin announces it. The contract
below is documented so the skill is ready to flip on at launch.

## What it is

A **host-agnostic**, editor-free MCP surface into the same StudioTwin cloud
backend, served directly by the StudioTwin API. Use it from any MCP client (agent,
pipeline, or a DCC without a dedicated connector). Because there is no editor, the
server returns **presigned URLs and asset references** rather than importing into
a scene; downstream, an in-engine connector (UE / Blender) can **import by asset
id**.

## Transport & auth

- **Streamable HTTP, stateless, tools-only.** One JSON-RPC message per `POST /mcp`;
  responses are plain JSON. `GET`/`DELETE` return `405` (no SSE stream, no
  sessions). Protocol version `2025-06-18` (older `2025-03-26` array batching is
  tolerated).
- **Auth: `x-api-key` header** carrying a StudioTwin API key — the same `st_` keys
  as `/jobs`; **organization keys are allowed**. Per-key rate limiting applies.
  Provide the key via the MCP client's server config/env — never in chat, logs, or
  tool arguments.
- On `initialize`, the server returns `serverInfo.name = "studiotwin"` and an
  `instructions` field with inline usage guidance, so any client gets baseline
  direction even without this skill.

## Tools

**Generation tools are auto-derived from the platform's VPFunction registry** —
one MCP tool per public function, with the **credit cost surfaced in the tool
description**. This is exactly why the skill never hardcodes tool names or costs:
discover them live via `tools/list`. Generation tools return a **job uuid
immediately** (asynchronous).

Fixed **platform tools** (stable names) wrap the job/asset/wallet lifecycle:

| Tool                              | Purpose                                                        |
| --------------------------------- | ------------------------------------------------------------- |
| `studiotwin_estimate_cost`        | Estimate a generation's credit cost + whether the balance covers it. Use before expensive gens. |
| `studiotwin_get_credit_balance`   | Current wallet balance.                                        |
| `studiotwin_get_job_status`       | Poll a job; outputs include presigned download URLs + asset references. |
| `studiotwin_list_my_jobs`         | Browse your jobs.                                              |
| `studiotwin_cancel_job`           | Cancel a queued/submitted job you own (refunds reserved credits). |
| `studiotwin_list_my_assets`       | Browse your asset library.                                    |
| `studiotwin_resolve_asset`        | Resolve an owned asset uuid → metadata + presigned download URL. |
| `studiotwin_upload_asset`         | Create a presigned S3 upload session.                         |
| `studiotwin_complete_asset_upload`| Finalize an upload into the asset library.                    |

Discover the live set at runtime; the table above is orientation, not a schema.

## Workflow (from the server's own instructions)

1. Optionally call `studiotwin_estimate_cost` and/or `studiotwin_get_credit_balance`.
2. Call a generation tool → receive a **job uuid** immediately.
3. Poll `studiotwin_get_job_status` until `COMPLETE` (submit once; do not re-fire).
4. Either **download** outputs via the returned presigned URLs, **or** pass the
   **asset id** to an in-engine StudioTwin integration (UE / Blender toolsets) to
   **import by asset id** — the cross-connector interchange contract.
5. Uploads: `studiotwin_upload_asset` → PUT to S3 → `studiotwin_complete_asset_upload`.

## Errors & credits

- **Code `40001` = wallet balance too low.** Surface it plainly, suggest
  `studiotwin_estimate_cost` / `studiotwin_get_credit_balance`, and route to
  [../onboarding/credits.md](../onboarding/credits.md) for topping up.
- Everything in the `SKILL.md` operating policy still holds: plan from intent,
  inspect the live definition, submit-once/poll, disclose costs, verify returned
  artifacts, never expose the API key or presigned URLs.

## Consumers

Remote-MCP outputs feed editor-free pipelines too — e.g. StudioTwin assets into
three.js / React-Three-Fiber (HDR env map as lighting, PBR maps into materials,
GLB meshes). That 3D-web composition is its own skill (tracked separately); this
connector is the generation source.

## References (internal)

- VPC-797 — Remote MCP MVP (`/mcp` Streamable HTTP, registry-derived tools). Done.
- VPC-799 — OAuth 2.1 + PKCE for public connector distribution. Planned (the
  `x-api-key` header is the MVP auth; OAuth is the later public-distribution path).
- VPC-796 — parent epic (remote server + in-engine agentic integration).
