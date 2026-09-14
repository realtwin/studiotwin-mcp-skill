# studiotwin-mcp-skill

An [AgentSkill](https://modelcontextprotocol.io) for operating **StudioTwin's** cloud asset-generation platform through MCP — animation, environments, meshes, materials, and audio for virtual production.

The skill is deliberately **operating guidance, not a tool catalog**: live MCP tool definitions are always the authority for names, schemas, costs, and limits. It never hardcodes or guesses them.

## Connectors

- **[Unreal Engine](references/connectors/ue-mcp.md)** — live, primary. StudioTwin UE plugin via Epic's Unreal MCP plugin, served locally inside the Editor.
- **[Web](references/connectors/web-mcp.md)** — live, host-agnostic, editor-free access to the same cloud backend. It can operate standalone and is also required by Blender.
- **[Blender](references/connectors/blender-mcp.md)** — released workflow using StudioTwin Web MCP for cloud activity, official Blender MCP for Editor access, and the StudioTwin Blender addon for supported downloaded files. Blender motion import is in progress.

## Layout

```
SKILL.md                              # entry: routing and operating policy
references/
  setup.md                            # canonical ordered setup router
  onboarding/
    account-api-key.md                # shared account, API key, and hygiene
    web-mcp.md                        # canonical hosted Web MCP connection setup
    unreal.md                         # StudioTwin UE plugin + Unreal MCP setup
    blender.md                        # official Blender MCP + StudioTwin addon setup
    credits.md                        # shared credits and cost expectations
    register.md                       # compatibility redirect for old links
    plugins.md                        # compatibility index for old links
  connectors/
    ue-mcp.md                         # UE runtime contract
    web-mcp.md                        # hosted Web MCP runtime contract
    blender-mcp.md                    # Blender orchestration and import behavior
  capabilities.md                    # map intent to capability family
  operations.md                      # async jobs, imports, Editor mutations
  content-guidance.md                # prompting and source preparation
  troubleshooting.md                 # failure diagnosis and recovery
```

## Onboarding via the skill

Incomplete setups always start at [references/setup.md](references/setup.md), which routes the user through one ordered path:

- Web: account/API key → Web MCP → Web runtime
- Blender: account/API key → Web MCP → Blender components → Blender runtime
- Unreal: account/API key → Unreal components → Unreal runtime

## Status

The Unreal Engine, Web MCP, and Blender setup routes are released and available. StudioTwin Blender motion import remains in progress.

## License

[MIT](./LICENSE) © 2026 RealTwin Solutions Inc.

---

_StudioTwin is a product of RealTwin Solutions Inc._
