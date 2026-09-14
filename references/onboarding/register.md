# Onboarding: StudioTwin account and API key

Use this shared first step before configuring any StudioTwin connector. The user performs account and credential actions; the agent guides them and verifies only what a connected surface reports.

## Register and select the workspace

Go to **[app.studiotwin.ai](https://app.studiotwin.ai)** and either sign in with Google or enter an email address and click **Continue**. No application form or waiting list is required.

Before creating a key, confirm the dashboard shows the intended workspace or organization. API keys belong to that organization, so select the one that should own the connector and its usage.

## Create an API key

From dashboard **[Get Started](https://app.studiotwin.ai/dashboard/get-started/)** or **[API Keys](https://app.studiotwin.ai/dashboard/api-keys)**:

1. Click **+ Create key**.
2. Give it a name. One key per machine or project is recommended so one key can be suspended without disrupting others.
3. Optionally add a description, then click **+ Create key**.
4. Copy the secret immediately. It is shown only once and cannot be retrieved again. StudioTwin API keys begin with `st_`.

The public [API Keys guide](https://docs.studiotwin.ai/docs/dashboard/pages/api-keys) is authoritative for the current dashboard flow.

## Protect and verify the key

- Never ask the user to paste the key into chat.
- Never place it in logs, source control, shared documents, tool arguments, Blender, or the skill.
- Store it only through the selected connector's documented credential mechanism.
- Watch for leading or trailing whitespace after copying.
- Suspend or delete a leaked key from the API Keys page. Suspension is reversible; deletion is permanent.
- If rejected, confirm the key begins with `st_`, is active, belongs to the intended organization, and contains no copied whitespace.

Continue with the connector-specific setup selected in [../setup.md](../setup.md). For credits and cost expectations, read [credits.md](credits.md).
