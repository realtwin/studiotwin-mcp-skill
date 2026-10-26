# studiotwin-mcp-skill

An [AgentSkill](https://modelcontextprotocol.io) for operating **StudioTwin's** cloud asset-generation platform through MCP — animation, environments, meshes, materials, and audio for virtual production.

The skill is deliberately **operating guidance, not a tool catalog**: live MCP tool definitions are always the authority for names, schemas, costs, and limits. It never hardcodes or guesses them.

## Connectors

- **[Unreal Engine](references/connectors/ue-mcp.md)** — live, primary. StudioTwin UE plugin via Epic's Unreal MCP plugin, served locally inside the Editor.
- **[Remote MCP](references/connectors/remote-mcp.md)** — live, host-agnostic, editor-free access to the same cloud backend. It can operate standalone and is also required by Blender.
- **[Blender](references/connectors/blender-mcp.md)** — released workflow using StudioTwin Remote MCP for cloud activity, official Blender MCP for Editor access, and the StudioTwin Blender addon for supported downloaded files. Blender motion import is in progress.

## Layout

```
SKILL.md                              # entry: routing and operating policy
references/
  setup.md                            # canonical ordered setup router
  onboarding/
    register.md                       # shared registration, workspace, API key, and hygiene
    remote-mcp.md                     # canonical hosted Remote MCP connection setup
    unreal.md                         # StudioTwin UE plugin + Unreal MCP setup
    blender.md                        # official Blender MCP + StudioTwin addon setup
    credits.md                        # shared credits and cost expectations
  connectors/
    ue-mcp.md                         # UE runtime contract
    remote-mcp.md                     # hosted Remote MCP runtime contract
    blender-mcp.md                    # Blender orchestration and import behavior
  destinations/
    threejs.md                        # three.js and WebGPU browser delivery
  capabilities.md                    # map intent to capability family
  operations.md                      # async jobs, imports, Editor mutations
  content-guidance.md                # prompting and source preparation
  troubleshooting.md                 # failure diagnosis and recovery
```

## Onboarding via the skill

Incomplete setups always start at [references/setup.md](references/setup.md), which routes the user through one ordered path:

- Remote MCP: [registration/API key](references/onboarding/register.md) → Remote MCP connection → Remote MCP runtime
- Blender: [registration/API key](references/onboarding/register.md) → Remote MCP connection → Blender components → Blender runtime
- Unreal: [registration/API key](references/onboarding/register.md) → Unreal components → Unreal runtime

## Status

The Unreal Engine, Remote MCP, and Blender setup routes are released and available. StudioTwin Blender motion import remains in progress.

## License

[MIT](./LICENSE) © 2026 RealTwin Solutions Inc.

---

_StudioTwin is a product of RealTwin Solutions Inc._
