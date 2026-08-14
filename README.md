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
- **Web / no-DCC** — draft, unconfirmed (host-agnostic access to the same backend).
- **Blender** — planned, not yet available.

## Layout

```
SKILL.md                          # entry: connectors, onboarding, operating policy
references/
  connectors/ue-mcp.md            # the live UE MCP surface
  connectors/web-mcp.md           # draft — pending confirmation
  connectors/blender-mcp.md       # stub — planned
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

UE connector + onboarding are populated from StudioTwin's docs. Web and Blender
connectors are stubs pending material. See open `[CONFIRM]` / `[TODO]` markers.

## License

TODO — to be set (MIT recommended for OSS).

---

_StudioTwin is a product of RealTwin Solutions Inc._
