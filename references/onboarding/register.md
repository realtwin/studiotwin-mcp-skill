# Onboarding: account & API key

Use this when a user reaches StudioTwin **through the skill** without an account
or API key yet. Guide them; never ask them to paste the key into chat, logs, or
source control. The user performs these steps — the agent explains and verifies
only what the connector reports.

## 1. Create an account

Go to **[app.studiotwin.ai](https://app.studiotwin.ai)** and either:

- **Sign in with Google**, or
- enter an **email address** and click **Continue**.

No application form, no waiting list.

## 2. Create an API key

From the dashboard **[Get Started](https://app.studiotwin.ai/dashboard/get-started/)**
page (also reachable from the sidebar and the Overview banner), or the
**[API Keys](https://app.studiotwin.ai/dashboard/api-keys)** page:

1. Click **+ Create key**.
2. Give it a **Name** (e.g. the machine or project — one key per machine/project
   is recommended so a single key can be suspended without disrupting others).
3. Optionally add a description, then **+ Create key**.
4. **Copy the secret immediately — it is shown only once and cannot be retrieved
   again.** Keys begin with `st_` and belong to your organization.

Docs: https://docs.studiotwin.ai/docs/dashboard/pages/api-keys

## 3. Connect the key to the connector

- **Unreal Engine:** install the plugin (see [plugins.md](plugins.md)), then
  **Edit → Project Settings → Plugins → StudioTwin**, paste the key into **Api
  Key**, and leave **Api Endpoint Url** empty. The key is validated there, so a
  mistyped/inactive key is flagged immediately. Watch for a trailing space from
  copying.
- **Web / other MCP connectors:** provide the `st_` key via the MCP server's
  env/config, never inline. (See [../connectors/web-mcp.md](../connectors/web-mcp.md);
  transport still to be confirmed.)

## 4. Verify

- **UE:** open the **Tools** menu — a **STUDIOTWIN** section should list the
  toolkits (Audio, Environment, Material, Mesh, Motion). If it is missing, the
  plugin is likely installed but not enabled (Edit → Plugins → enable → Restart).
- A quick end-to-end check: **Tools → Motion Toolkit → Text to Motion** with a
  short prompt (e.g. "A person is walking forward hastily"). It needs nothing in
  the project (the plugin ships its own skeletal mesh) and confirms the full cloud
  round-trip. It costs credits (see [credits.md](credits.md)).

If a key is rejected: check for whitespace, confirm the key is **Active** (not
suspended) on the API Keys page, confirm it belongs to the expected organization,
and confirm the endpoint field is empty.

## Key hygiene (tell the user)

- Never commit keys to public repos, shared docs, or logs.
- One key per machine/project; suspend or delete a leaked key from the API Keys
  page (suspend is reversible; delete is permanent).
