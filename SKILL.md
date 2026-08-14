---
name: studiotwin-mcp-skill
description: >-
  Use StudioTwin's generative 3D/asset pipeline through its MCP server — turn
  text and images into motion, 3D meshes, environments, and PBR materials for
  Unreal Engine and virtual-production workflows. Use when a user wants to
  generate game-ready 3D assets, animate characters from text, convert images to
  3D or to environment splats, or produce tileable materials, and has (or wants
  to connect) a StudioTwin account.
license: TODO — confirm with Victor (MIT recommended for OSS)
---

# StudioTwin MCP Skill

> **v1 DRAFT — 2026-08-14.** Scaffold pending Victor's follow-up material on the
> MCP server (tool names, auth flow, endpoints). Sections marked **[TODO]** are
> placeholders to be filled from that material, not verified facts.

## What this skill does

StudioTwin exposes its generative asset pipeline over the Model Context Protocol
(MCP). This skill teaches an agent how to connect to the StudioTwin MCP server,
authenticate, invoke the generation tools, poll long-running jobs, and hand the
resulting assets to Unreal Engine (via the StudioTwin plugin) or other DCC tools.

## When to use it

- Generate a game-ready **3D mesh** from a single image (Image → 3D).
- Generate an explorable **environment** (splat/scene) from an image (Image → Env).
- Animate a character or object from a text prompt (**Text → Motion**).
- Produce a tileable **PBR material** from a texture reference (Texture → Material).
- Chain the above into a scene and place assets in Unreal via the plugin.

## Prerequisites

1. A StudioTwin account and an **organization API key** (from the dashboard).
2. Available **credits** — generation is metered (see cost table below).
3. The StudioTwin MCP server configured in the host (Claude Code / OpenClaw / etc.).

## Connecting the MCP server

**[TODO — from Victor's material]** Exact server command / URL, transport
(stdio vs. SSE/HTTP), and env vars. Expected shape:

```jsonc
// mcp config (illustrative — confirm against real server)
{
  "mcpServers": {
    "studiotwin": {
      "command": "npx",
      "args": ["-y", "@studiotwin/mcp-server"],
      "env": { "STUDIOTWIN_API_KEY": "sk_org_..." }
    }
  }
}
```

## Tools (mapped to StudioTwin generation types)

> Tool names below are **[TODO placeholders]** — replace with the server's actual
> tool identifiers once known. Credit costs are the current published product
> figures.

| Capability        | Input            | Output              | Credits |
| ----------------- | ---------------- | ------------------- | ------: |
| Text → Motion     | text prompt      | animation           |       5 |
| Image → 3D        | image            | 3D mesh             |      25 |
| Image → Env       | image            | environment / splat |      25 |
| Texture → Material| texture image    | tileable PBR set    |      20 |

Each generation tool is expected to be **asynchronous**: it returns a job id;
poll for status until the asset URL is ready. **[TODO]** confirm poll tool +
status enum.

## Typical flow

1. Confirm the user's intent and which capability it maps to.
2. Check credits are sufficient for the operation.
3. Call the generation tool with the prompt/image and any parameters.
4. Poll the job until complete; surface progress to the user.
5. Return the asset URL / download, and offer plugin hand-off for Unreal.

## Errors & limits

**[TODO]** rate limits, max input resolution, supported formats, failure/retry
semantics, credit-refund-on-failure behavior.

## References

- StudioTwin dashboard & docs — **[TODO: link]**
- StudioTwin Unreal plugin — **[TODO: link]**
- MCP spec: https://modelcontextprotocol.io
