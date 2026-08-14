# StudioTwin UE MCP setup

Use this reference when onboarding a user or when MCP discovery fails. Keep setup user-led: the agent explains the steps, attempts connection and discovery, and reports what remains unresolved.

## What is required

1. **A compatible Unreal Editor project.**
2. **The StudioTwin UE plugin.**
3. **The Unreal MCP plugin** (`ModelContextProtocol`), plus the **optional All Toolsets plugin** when the user also wants Unreal's default toolsets. Unreal MCP enables the Toolset Registry dependency automatically.
4. **An MCP client configuration** generated for the user's agent or client.

Use Epic's [Unreal MCP documentation](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-mcp-in-unreal-editor#optional:one-shotusingtheterminalplugin) as the authority for Unreal MCP setup and client configuration.

## 1. Install StudioTwin — User

If StudioTwin is not installed, direct the user to either:

- [StudioTwin plugin installation guide](https://docs.studiotwin.ai/docs/plugin/installation/) for detailed instructions and version guidance.
- [StudioTwin on Fab](https://www.fab.com/listings/db820954-ce06-47de-bdc0-054b669c1727) to obtain the plugin through Fab and the Epic Games Launcher.

The StudioTwin build must match the exact Unreal Engine version used by the project. After installation, ask the user to open **Edit → Plugins**, enable **StudioTwin**, and restart Unreal Editor when prompted.

## 2. Create an account and configure the API key — User

If the user does not have a StudioTwin account or API key, direct them to [StudioTwin Get Started](https://app.studiotwin.ai/dashboard/get-started/).

After signing up and creating an API key:

1. Open **Edit → Project Settings** in Unreal Editor.
2. Under **Plugins**, select **StudioTwin**.
3. Paste the key into the **API Key** field.
4. Leave **API Endpoint URL** empty unless StudioTwin support or deployment documentation specifies otherwise.
5. Confirm Unreal accepts the key.

The agent must not ask the user to paste the API key into chat, logs, source control, or the skill. If validation fails, ask the user to check whitespace, key status, organization, and endpoint settings using the [StudioTwin installation guide](https://docs.studiotwin.ai/docs/plugin/installation/).

## 3. Enable Unreal MCP — User

1. Open **Edit → Plugins**.
2. Search for **Unreal MCP** and enable it.
3. Optionally enable **All Toolsets** to expose Unreal's default toolsets in addition to StudioTwin's toolsets.
4. Restart Unreal Editor when prompted.

The plugin identifier and console-command prefix are `ModelContextProtocol`. The Toolset Registry is a dependency of Unreal MCP and is enabled automatically.

## 4. Start the MCP server — User

Open the Unreal Editor console and run:

```
ModelContextProtocol.StartServer
```

To specify a port explicitly:

```
ModelContextProtocol.StartServer 8000
```

The documented default endpoint is `http://127.0.0.1:8000/mcp`. This is a local connection without an authentication layer; do not expose it for remote access.

The user may configure automatic server startup later through **Editor Preferences → General → Model Context Protocol → Auto Start Server**. The console command is the simplest first-run path.

## 5. Generate the MCP client configuration — User

From the Unreal Editor console, generate the configuration for the user's client:

```
ModelContextProtocol.GenerateClientConfig Codex
```

Epic documents these client values: `ClaudeCode`, `Cursor`, `VSCode`, `Gemini`, `Codex`, and `All`. Use `All` when several clients need configuration:

```
ModelContextProtocol.GenerateClientConfig All
```

The command writes the appropriate configuration in the project or workspace root. JSON configurations are merged with existing entries. Epic documents the Codex TOML configuration as write-once, so an existing stale entry may require user review before regeneration.

## 6. Connect and discover — Shared

1. **User:** Start the agent or MCP client from the project or workspace root where Unreal generated the configuration.
2. **Agent:** Attempt to connect and list the live MCP tools.
3. **Agent:** Confirm the StudioTwin capability groups are present.
4. **Agent:** Use the live tool definitions as the authority for names, schemas, costs, and behavior.

If connection or discovery fails, the agent must report the failure and guide the user through these checks:

- Unreal Editor and the intended project are open.
- The MCP server was started successfully.
- StudioTwin and Unreal MCP are enabled.
- The client was launched from the root containing the generated configuration.
- The generated endpoint matches the Unreal MCP server.
- The StudioTwin API key is valid.

Retry discovery only after the user confirms the relevant host-side correction.

## Responsibility summary

- **User:** installs plugins, signs up, configures the API key, enables plugins, runs Unreal console setup commands, and launches the client from the correct root.
- **Agent:** attempts connection, discovers tools, inspects live definitions, and reports success or failure.
- **Agent guides user:** provides the appropriate documentation link and precise corrective steps; it never claims that a user-owned Unreal setup action was completed.
