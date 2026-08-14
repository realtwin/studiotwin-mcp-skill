# Connector: Web MCP (host-agnostic / no DCC)

**Status: draft — pending StudioTwin material (2026-08-14).** The shape below is
inferred from how StudioTwin's cloud service works; confirm every bracketed item
against the real connector before relying on it.

## What it is (intended)

A **host-agnostic** StudioTwin MCP surface that reaches the same cloud generation
backend as the plugins, but **without** an Unreal/Blender editor in the loop. Use
it when a user wants StudioTwin assets in an agent, a pipeline, or a DCC that has
no dedicated connector. Because there is no editor:

- Outputs are **files / signed URLs** (e.g. GLB meshes, HDR/EXR environment maps,
  PBR texture sets, animation data, audio) — **not** in-editor imports.
- There is **no level or sequence mutation**; the "verify the result" step checks
  returned artifacts and download integrity, not UE object paths.
- Everything else in the `SKILL.md` operating policy still holds: plan from
  intent, inspect the live definition, submit-once/poll for async jobs, disclose
  costs, never expose the API key or signed URLs.

## Auth & transport — **[CONFIRM]**

- Auth is expected to use a StudioTwin **`st_` API key** (organization-scoped, the
  same key type as the plugins). Provide it via the host's MCP server env/config,
  **never** in chat, logs, or tool arguments.
- Transport / endpoint: **[unknown — hosted remote SSE/HTTP URL? local bridge?
  package name?]**. Discover live tools; do not assume names or schemas.

## Open questions for StudioTwin (blocking full write-up)

1. Does the Web MCP connector exist today, or is it roadmap?
2. What is the endpoint + transport (remote hosted URL vs. a local `npx`/binary
   bridge), and the exact server config an MCP client needs?
3. Is auth the `st_` org API key, or a different token/OAuth flow?
4. Which capability groups are exposed (full parity with the toolkits, or a
   subset), and are the credit costs identical to the plugin?
5. Output contract: formats returned, URL lifetime/expiry, and any download step
   the agent is expected to perform.

Until answered, **do not tell a user the Web connector is available**; offer the
live Unreal connector ([ue-mcp.md](ue-mcp.md)) instead, and route account/API-key
setup through [../onboarding/register.md](../onboarding/register.md).
