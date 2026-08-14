# studiotwin-mcp-skill

An [AgentSkill](https://modelcontextprotocol.io) for operating **StudioTwin's**
cloud asset-generation platform through MCP — animation, environments, meshes,
materials, and audio for virtual production.

The skill is deliberately **operating guidance, not a tool catalog**: live MCP
tool definitions are always the authority for names, schemas, costs, and limits.
It never hardcodes or guesses them.

## Connectors

- **Unreal Engine** — live, primary. StudioTwin UE plugin via Epic's Unreal MCP
  plugin, served locally inside the Editor.
- **Remote (web)** — built, not yet public. Host-agnostic, editor-free access to
  the same cloud backend (`POST /mcp`, `x-api-key`); launches with Blender.
- **Blender** — in development, not yet launched. Thin addon over the remote MCP
  that imports StudioTwin assets by id.

## Layout

```
SKILL.md                          # entry: connectors, onboarding, operating policy
references/
  connectors/ue-mcp.md            # the live UE MCP surface
  connectors/web-mcp.md           # remote /mcp — built, not yet public
  connectors/blender-mcp.md       # thin addon over remote MCP — in development
  onboarding/register.md          # account + API key (st_…)
  onboarding/plugins.md           # download/install the right plugin
  onboarding/credits.md           # credits, free allocation, cost expectations
  setup.md                        # UE install / MCP server / client config / first run
  capabilities.md                 # map intent → capability family
  operations.md                   # async jobs, imports, Editor mutations
  content-guidance.md             # prompting & source preparation
  troubleshooting.md              # failure diagnosis & recovery
```

## Onboarding via the skill

Users can enter the StudioTwin customer journey through this skill: it walks them
to [app.studiotwin.ai](https://app.studiotwin.ai) to register, create an API key,
install the plugin for their host, and run a first generation.

## Status

UE connector + onboarding are populated from StudioTwin's docs and are usable
today. The remote (web) and Blender connectors are documented from the platform's
internal design but are not yet public — they launch together.

## License

[MIT](./LICENSE) © 2026 RealTwin Solutions Inc.

---

_StudioTwin is a product of RealTwin Solutions Inc._
