---
name: "studiotwin-mcp"
description: "Operate StudioTwin's cloud asset-generation platform — environment maps, PBR materials, 3D meshes, character motion, sound effects — through MCP. Covers the live Unreal Engine, Web MCP, and Blender addon workflows, plus onboarding a user into StudioTwin. Use when generating assets for Unreal, Blender, or 3D-web/virtual-production work."
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
- **Web — live.** Host-agnostic, editor-free access to the same cloud backend. It can operate standalone and is also required by Blender. [references/connectors/web-mcp.md](references/connectors/web-mcp.md)
- **Blender — released.** StudioTwin Web MCP handles cloud activity, official Blender MCP provides Editor access, and the StudioTwin Blender addon applies supported downloaded files. [references/connectors/blender-mcp.md](references/connectors/blender-mcp.md)

Regardless of connector, the operating policy below applies. If the host exposes a StudioTwin surface not yet documented here, connect, discover live tools, and treat those definitions as authoritative.

**Asset reuse across hosts.** StudioTwin generation and import are separable: a generation produces an **asset** with a uuid. Reuse that asset rather than regenerating it merely to change hosts. A connector may import by asset id or resolve and download the asset for a local importer; follow the live connector contract.

## Onboarding a new user

If any account, credential, connection, plugin, or addon setup is incomplete, follow one complete route below. [references/setup.md](references/setup.md) remains the shared router and verification reference; the linked onboarding pages own setup details. The user performs setup actions; the agent explains, attempts connection and discovery, and verifies only what the connected surface reports. Never ask for the API key in chat.

- **Web / no DCC:** [register and create an API key](references/onboarding/register.md) → [Web MCP connection](references/onboarding/web-mcp.md) → [Web MCP runtime](references/connectors/web-mcp.md). Web can operate standalone.
- **Blender:** [register and create an API key](references/onboarding/register.md) → [Web MCP connection](references/onboarding/web-mcp.md) → [official Blender MCP and StudioTwin Blender addon setup](references/onboarding/blender.md) → [Blender runtime](references/connectors/blender-mcp.md). Do not skip Web MCP: it owns Blender's StudioTwin cloud operations.
- **Unreal Engine:** [register and create an API key](references/onboarding/register.md) → [StudioTwin UE plugin and Unreal MCP setup](references/onboarding/unreal.md) → [Unreal runtime](references/connectors/ue-mcp.md). This route is independent of Web MCP and requires Unreal Engine 5.8 or newer plus StudioTwin `3.0.0+` for native Unreal MCP; the plugin build must match the engine version.

## Start every session

**Orient first — work out where you are** by discovering the live MCP tools and reading the surface, then route:

- StudioTwin toolkits behind Epic's Unreal MCP (`ModelContextProtocol`), local `127.0.0.1:8000/mcp`, tools that mutate an open project → **Unreal Engine**: [references/connectors/ue-mcp.md](references/connectors/ue-mcp.md).
- `studiotwin_*` platform tools over remote MCP with no editor → **Web**: [references/connectors/web-mcp.md](references/connectors/web-mcp.md).
- Hosted `studiotwin_*` platform tools plus official Blender MCP tools such as `execute_blender_code`, with Blender changes performed through the StudioTwin Blender addon → **Blender**: [references/connectors/blender-mcp.md](references/connectors/blender-mcp.md).
- No StudioTwin tools, or any required layer is incomplete → route through [references/setup.md](references/setup.md). Do not assume Unreal.

Once placed on an already connected surface, route directly to its connector runtime file above. Pull other references only as needed: [capabilities](references/capabilities.md), [operations](references/operations.md), [content-guidance](references/content-guidance.md), [troubleshooting](references/troubleshooting.md).

Found a StudioTwin surface but it will not connect or lists no tools? Read [references/troubleshooting.md](references/troubleshooting.md), then return to the relevant canonical onboarding page. A UE plugin below `3.0.0`, for example, runs as a toolkit but exposes no MCP tools; follow [Unreal onboarding](references/onboarding/unreal.md).

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
