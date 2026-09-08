---
name: "studiotwin-mcp"
description: "Operate StudioTwin's cloud asset-generation platform — environment maps, PBR materials, 3D meshes, character motion, sound effects — through MCP. Covers the live Unreal Engine connector today, plus the remote (web) and Blender connectors as they land, and onboarding a user into StudioTwin. Use when generating assets for Unreal, Blender, or 3D-web/virtual-production work."
author: RealTwin Solutions Inc.
version: "1.3.0"
license: MIT
---

# StudioTwin MCP

Use StudioTwin through the MCP tools exposed by a connected host. Treat the live MCP tool definitions as the authority for tool names, inputs, outputs, defaults, limits, and availability. Do not reproduce or infer those definitions from this skill.

> **Full skill:** this is one file of a multi-file skill — the `references/…` links are files in the repo, not inline text. Clone the whole thing before acting: `git clone https://github.com/realtwin/studiotwin-mcp-skill` (or fetch each path from `https://raw.githubusercontent.com/realtwin/studiotwin-mcp-skill/main/`).

## Connectors

StudioTwin reaches the same cloud generation backend through different MCP surfaces. Select the one the host actually exposes and read its reference:

- **Unreal Engine — live, primary.** StudioTwin UE plugin via Epic's Unreal MCP plugin, served locally inside the Editor. [references/connectors/ue-mcp.md](references/connectors/ue-mcp.md)
- **Remote (web) — built, not yet public.** Host-agnostic, editor-free access to the same cloud backend (`POST /mcp`, `x-api-key`); in internal review, launches with Blender. Do not present as available yet. [references/connectors/web-mcp.md](references/connectors/web-mcp.md)
- **Blender — in development, not yet launched.** Hosted StudioTwin MCP for generation, official Blender MCP for Editor access, and an importer-only StudioTwin extension for applying downloaded local files. [references/connectors/blender-mcp.md](references/connectors/blender-mcp.md)

Regardless of connector, the operating policy below applies. If the host exposes a StudioTwin surface not yet documented here, connect, discover live tools, and treat those definitions as authoritative.

**Asset reuse across hosts.** StudioTwin generation and import are separable: a generation produces an **asset** with a uuid. Reuse that asset rather than regenerating it merely to change hosts. A connector may import by asset id or resolve and download the asset for a local importer; follow the live connector contract.

## Onboarding a new user

If the user has no StudioTwin account, API key, or installed plugin, guide them (they act; you explain and verify only what the connector reports) — never ask for the API key in chat:

- Account + API key (`st_…`, shown once): [references/onboarding/register.md](references/onboarding/register.md)
- Download/install the right plugin for the connector: [references/onboarding/plugins.md](references/onboarding/plugins.md)
- Credits, the free monthly allocation, and cost expectations: [references/onboarding/credits.md](references/onboarding/credits.md)

## Start every session

**Orient first — work out where you are** by discovering the live MCP tools and reading the surface, then route:

- StudioTwin toolkits behind Epic's Unreal MCP (`ModelContextProtocol`), local `127.0.0.1:8000/mcp`, tools that mutate an open project → **Unreal Engine**: [references/connectors/ue-mcp.md](references/connectors/ue-mcp.md).
- `studiotwin_*` platform tools over a remote `/mcp` with `x-api-key`, no editor → **Web (remote)**: [references/connectors/web-mcp.md](references/connectors/web-mcp.md) — not public yet.
- Hosted `studiotwin_*` platform tools plus official Blender MCP tools such as `execute_blender_code`, with Blender changes performed through the StudioTwin importer extension → **Blender**: [references/connectors/blender-mcp.md](references/connectors/blender-mcp.md) — not launched yet.
- No StudioTwin tools at all → nothing is connected here. Don't assume Unreal; help the operator get set up — [references/onboarding/register.md](references/onboarding/register.md) covers how to place them and what to ask.

Once you've placed yourself, treat the live definitions as the authority and pull only the reference the request needs: [setup](references/setup.md), [capabilities](references/capabilities.md), [operations](references/operations.md), [content-guidance](references/content-guidance.md), [troubleshooting](references/troubleshooting.md).

Found a StudioTwin surface but it won't connect or lists no tools? That's setup, not orientation — read [references/troubleshooting.md](references/troubleshooting.md). (Common one: a UE plugin below `3.0.0` runs as a toolkit but exposes no MCP tools — update from Fab, see [references/onboarding/plugins.md](references/onboarding/plugins.md).)

Never claim to have verified host or plugin state unless the live surface shows it. Never guess a tool name or schema.

## Operating policy

### Plan from intent

Translate the request into a deliverable and the smallest sequence of discovered capabilities. Distinguish:

- paid generation from local Editor operations;
- generation/import from level or sequence mutation;
- independent stages from stages that consume earlier outputs;
- requested work from optional variants, retries, saving, rendering, or scene changes.

Do not treat authorization for one stage as authorization for extra generations, batches, retries, level mutations, saves, or renders.

### Inspect before acting

Read the selected live tool definition immediately before calling it. Validate required inputs, accepted path forms, constraints, cost information, async behavior, and output contract from the live definition. UE object paths, local paths, URIs, and cloud references are not interchangeable unless the live definition explicitly says so.

If a paid operation has no live cost estimate or formula, state that the cost is unknown before requesting authorization. Never copy pricing from another connector.

### Execute incrementally

For asynchronous work:

1. Submit once.
2. Preserve every returned identifier and label submission, status, generation,
   and output identifiers separately when the connector returns more than one.
3. Poll that existing submission at the recommended interval.
4. Do not resubmit merely because a transport response was lost or ambiguous.
5. Continue dependent work only after verified successful completion.

For synchronous Editor operations, verify the target project or Blender scene, destination, source paths, and mutation scope before calling. Treat imports and level, sequence, or Blender scene edits as state-changing operations.

Parallelize only independent, explicitly authorized work.

### Verify the result

Do not equate a terminal job state with a complete deliverable. Inspect returned warnings and notes, then verify the expected host assets, paths, classes or datablocks, roles, level actors, sequences, or animation results. Report partial imports and missing roles honestly.

Do not claim that an asset was saved, persisted, transactionally undoable, or collision-free unless verified in the current environment.

### Report concisely

Report:

- capability and discovered tool used;
- submission job id and any distinct status, generation, or output identifiers;
- source and resulting asset ids, local paths, UE object paths, or Blender datablocks as applicable;
- level, sequence, or Blender scene mutations;
- warnings, missing artifacts, or partial success;
- what was verified and what remains unverified.

Never expose credentials, API keys, signed URLs, or other transient secrets.

## Completion checklist

- Live tools were discovered and their current definitions were followed.
- Paid work and Editor mutations stayed within the authorized scope.
- Async work was submitted once and polled by the original submission identifier.
- Dependent stages ran in order.
- Expected host artifacts and scene changes were verified.
- Partial success, warnings, costs, and unresolved persistence behavior were disclosed.
