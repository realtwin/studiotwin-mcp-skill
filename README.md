# studiotwin-mcp-skill

An [AgentSkill](https://modelcontextprotocol.io) for operating **StudioTwin's** cloud asset-generation platform through MCP — animation, environments, meshes, materials, and audio for virtual production.

The skill is deliberately **operating guidance, not a tool catalog**: live MCP tool definitions are always the authority for names, schemas, costs, and limits. It never hardcodes or guesses them.

## Connectors

- **[Unreal Engine](references/connectors/ue-mcp.md)** — live, primary. StudioTwin UE plugin via Epic's Unreal MCP plugin, served locally inside the Editor.
- **[Remote MCP](references/connectors/remote-mcp.md)** — live, host-agnostic, editor-free access to the same cloud backend. It can operate standalone and is also required by Blender.
- **[Blender](references/connectors/blender-mcp.md)** — released workflow using StudioTwin Remote MCP for cloud activity, official Blender MCP for Editor access, and the StudioTwin Blender addon for supported downloaded files. Blender motion import is in progress.

## Install in an agent harness

This repository is also an installable bundle for Claude Code, Codex, and OpenCode. Each bundle contains two things only: a direct connection to the StudioTwin Remote MCP endpoint `https://api.studiotwin.ai/mcp`, and this skill. It has no local proxy, helper process, or UI.

Every harness authenticates the same way: your StudioTwin `st_...` API key is sent as the `x-api-key` header. To create a key, follow [registration and API-key creation](references/onboarding/register.md). Never paste the key into chat or commit it to a repository.

### Claude Code

Add this repository as a plugin marketplace, then install the plugin:

```text
/plugin marketplace add realtwin/studiotwin-mcp-skill
/plugin install studiotwin-mcp@studiotwin
```

Claude Code asks for your **StudioTwin API key** when the plugin is enabled. The field is marked sensitive, so Claude Code keeps the key in your system credential store instead of `settings.json`. To set or change it later, run `/plugin configure studiotwin-mcp@studiotwin`. Restart Claude Code after changing the key.

The plugin provides:

- the MCP server `studiotwin`, defined in [`.mcp.json`](.mcp.json);
- the skill `studiotwin-mcp:studiotwin-mcp`, which is this repository's `SKILL.md` and `references/`.

Run `/mcp` to confirm that `plugin:studiotwin-mcp:studiotwin` shows as connected.

### Codex

Add this repository as a plugin marketplace, then install the plugin:

```bash
codex plugin marketplace add realtwin/studiotwin-mcp-skill
codex plugin add studiotwin-mcp@studiotwin
```

You can also install it from the `/plugins` browser in the Codex CLI. Codex reads the key from the `STUDIOTWIN_API_KEY` environment variable and sends it as `x-api-key` (see [`codex.mcp.json`](codex.mcp.json)). Export that variable in the environment that launches Codex, then start a new session. `codex mcp list` should show the `studiotwin` server, and the skill appears as `studiotwin-mcp:studiotwin-mcp`.

To skip the plugin and configure Codex by hand, add the server to `~/.codex/config.toml`:

```toml
[mcp_servers.studiotwin]
url = "https://api.studiotwin.ai/mcp"
env_http_headers = { "x-api-key" = "STUDIOTWIN_API_KEY" }
```

Then install the skill with `npx --yes skills@latest add realtwin/studiotwin-mcp-skill --skill studiotwin-mcp --global --agent codex`.

### OpenCode

OpenCode plugins are JavaScript code packages, and OpenCode has no manifest-only bundle format, so OpenCode setup is a config snippet plus a skill install.

1. Merge [`opencode/opencode.json`](opencode/opencode.json) into your global `~/.config/opencode/opencode.json`, or into a project's `opencode.json`:

   ```json
   {
     "$schema": "https://opencode.ai/config.json",
     "mcp": {
       "servers": {
         "studiotwin": {
           "type": "remote",
           "url": "https://api.studiotwin.ai/mcp",
           "oauth": false,
           "headers": { "x-api-key": "{env:STUDIOTWIN_API_KEY}" }
         }
       }
     }
   }
   ```

   `oauth: false` is required. StudioTwin uses an API key, and without this setting OpenCode tries an OAuth sign-in. OpenCode V2 groups servers under `mcp.servers`. Older OpenCode V1 configs put `studiotwin` directly under `mcp` instead.

2. Install the skill:

   ```bash
   npx --yes skills@latest add realtwin/studiotwin-mcp-skill --skill studiotwin-mcp --global --agent opencode
   ```

   Alternatively, clone this repository to `~/.config/opencode/skills/studiotwin-mcp`.

3. Export `STUDIOTWIN_API_KEY` in the environment that launches OpenCode, then run `opencode mcp list`. It should report `studiotwin connected`.

Notes on submitting the bundle to each harness's public registry are in [docs/registry-listing.md](docs/registry-listing.md).

## Layout

```
.claude-plugin/plugin.json            # Claude Code plugin manifest
.claude-plugin/marketplace.json       # Claude Code marketplace (also read by Codex)
.mcp.json                             # Claude Code MCP server: Remote MCP + x-api-key from userConfig
.codex-plugin/plugin.json             # Codex plugin manifest
.agents/plugins/marketplace.json      # Codex marketplace
codex.mcp.json                        # Codex MCP server: Remote MCP + x-api-key from STUDIOTWIN_API_KEY
opencode/opencode.json                # OpenCode MCP config snippet
docs/registry-listing.md              # where and how to submit to each registry
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
