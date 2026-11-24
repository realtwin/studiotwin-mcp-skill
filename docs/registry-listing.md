# Registry listing notes

These notes cover where the StudioTwin bundle could be listed for each harness and what each listing requires. Nothing here has been submitted. A public listing is an external commitment and needs RealTwin's approval first.

Sources were checked on 2026-11-24. Registry rules change often, so recheck each linked page before submitting.

## Read this first: authentication

StudioTwin Remote MCP authenticates with an `x-api-key` header carrying the user's `st_...` key. Both large public directories currently expect a remote MCP server to use **OAuth or no authentication**:

- **OpenAI (ChatGPT and Codex plugin directory):** the submission portal's connection form "supports no authentication, OAuth, and mixed unauthenticated/OAuth access". It has no API-key or bearer-token flow, and submissions with non-empty server `headers` are rejected. OpenAI's guidance is to contact them if a submission requires API-key authentication. [Authentication limitations](https://developers.openai.com/plugins/deploy/submission#authentication-limitations)
- **Anthropic (Claude directory):** connector submissions should "use OAuth 2.0 for authenticated services". The pre-submission checklist blocks credentials in MCP headers and asks plugins to collect each credential through a sensitive `userConfig` entry, which this bundle already does. [Connector submission](https://claude.com/docs/connectors/building/submission), [plugin pre-submission checklist](https://claude.com/docs/plugins/pre-submission-checklist)

The self-hosted marketplaces in this repository work today with the API key. A listing in either public directory most likely needs one of these first:

1. OAuth 2.0 support on `https://api.studiotwin.ai/mcp` (the endpoint currently answers `404` on `/register`, so it has no dynamic client registration); or
2. an exception agreed directly with OpenAI or Anthropic for API-key authentication.

This is a product decision for RealTwin. The bundle doesn't need to change for either option, apart from removing the header once OAuth exists.

## Claude Code

| | |
| --- | --- |
| Self-hosted (works now) | This repository is a Claude Code marketplace: `/plugin marketplace add realtwin/studiotwin-mcp-skill`, then `/plugin install studiotwin-mcp@studiotwin`. |
| Public listing | Anthropic's directory at [claude.ai/directory](https://claude.ai/directory). Submit through the developer portal at [claude.ai/directory/manage](https://claude.ai/directory/manage). One listing covers claude.ai, Cowork, and Claude Code. |
| Official marketplace | `claude-plugins-official` doesn't take portal submissions. You need an Anthropic partner contact to be listed there. |
| Guide | [Submit your plugin](https://claude.com/docs/plugins/submit), [pre-submission checklist](https://claude.com/docs/plugins/pre-submission-checklist) |

Requirements and current status:

- **Account:** a paid claude.ai plan. On Team or Enterprise, an Owner submits, or a member with the **Directory** permission. The first organization to submit a repository folder owns that listing, so submit from the organization that should own it long term.
- **GitHub:** the submitter's GitHub account must be connected on claude.ai and able to push to `realtwin/studiotwin-mcp-skill`. The repository must be public before the listing goes live; it is already public.
- **Local validation:** `claude plugin validate . --strict`. This currently fails only on `ICON_MISSING`.
- **Icon (missing):** a square PNG or JPEG, 512 to 2048 px, under 2 MB, at `.claude-plugin/icon.png` or referenced by `icon` in `plugin.json`. The portal uses the icon only the first time the plugin is saved or submitted, so add the final artwork before the first portal save.
- **README:** at least 40 words outside code blocks. Already met.
- **License:** `LICENSE` file or a `license` field. Already met (MIT).
- **Listing URLs:** `privacyPolicyUrl`, `termsOfServiceUrl`, and `documentationUrl` are set in `plugin.json`.
- **MCP:** a remote server with `type: http` and an absolute `https://` URL, and no literal credential in headers. Already met: the header value is `${user_config.studiotwin_api_key}`.
- **Connector listing:** Anthropic asks owners of a remote MCP server to also submit that server as its own **MCP connector**, then pair it with the plugin. That connector submission is where the OAuth requirement applies.
- **Data handling questionnaire:** covers personal data, third-party data flows, retention, and use by people under 18.
- **Review:** an automated safety scan plus policy review. A version that passes can then be published.

## Codex (OpenAI plugin directory)

| | |
| --- | --- |
| Self-hosted (works now) | This repository is a Codex marketplace (`.agents/plugins/marketplace.json`): `codex plugin marketplace add realtwin/studiotwin-mcp-skill`, then `codex plugin add studiotwin-mcp@studiotwin`. |
| Public listing | The universal plugin directory shared by ChatGPT and Codex. Upload a plugin ZIP at [platform.openai.com/plugins](https://platform.openai.com/plugins). |
| Guide | [Package your plugin](https://developers.openai.com/plugins/build/plugins), [Upload and submit your plugin](https://developers.openai.com/plugins/deploy/submission), [plugin guidelines](https://developers.openai.com/plugins/plugin-guidelines) |

Requirements and current status:

- **Account:** an OpenAI Platform organization. Owners can submit; other members need **Apps Management Write**. To publish under the company name, complete business verification.
- **Package:** a ZIP of the plugin. The bundle here uses the Codex compatibility manifest (`.codex-plugin/plugin.json` plus `codex.mcp.json`), which Codex supports. For a public submission, OpenAI recommends the portable Agent Plugins layout: root `plugin.json` plus `mcp.json` with `$schema: https://agent-plugins.org/schemas/1.0.0/...`. That layout can't carry `env_http_headers`, so it has no way to send an API key from the user's environment. It fits only once the server supports OAuth (see above).
- **MCP connection:** submit the remote HTTPS endpoint through **With MCP**. The portal scans the server's tools after connecting, which requires an authentication method the portal supports.
- **Review materials:** five positive test cases, three negative test cases, a video walkthrough URL, release notes, and reviewer credentials for a dedicated test account with sample data and no MFA. Test cases, the video URL, and release notes can be supplied in `plugin.json`; credentials are entered in the portal.
- **Assets:** `composerIcon` and `logo` under `./assets/` (not yet included). Public URLs must resolve and identify the same publisher.
- **Not allowed in public plugins:** lifecycle hooks (this bundle has none) and secrets in the ZIP.
- **Updates after publication:** changes to the MCP server are picked up automatically. Changes to the skill or metadata need a new ZIP.

## OpenCode

OpenCode has no central plugin or MCP registry. Its plugins are JavaScript code packages, so a configuration-only bundle like this one isn't an OpenCode plugin. Users add the MCP config snippet and install the skill, as the README describes.

Discovery channels:

- **OpenCode ecosystem page:** [opencode.ai/docs/ecosystem](https://opencode.ai/docs/ecosystem). The page says "Want to add your OpenCode related project to this list? Submit a PR." The docs live in [`anomalyco/opencode`](https://github.com/anomalyco/opencode), which `sst/opencode` now redirects to.
- **awesome-opencode:** [github.com/awesome-opencode/awesome-opencode](https://github.com/awesome-opencode/awesome-opencode). Community list; add an entry by PR.
- **opencode.cafe:** a community aggregator linked from the ecosystem page.
- **skills.sh / `npx skills`:** the skill already installs with `npx --yes skills@latest add realtwin/studiotwin-mcp-skill --skill studiotwin-mcp`. That covers OpenCode, Codex, Claude Code, and other agents without a separate listing.

The requirements are light: a public repository, a clear README, and a license. They are already met.

## Approval checklist before any submission

- [ ] Victor approves listing in each registry.
- [ ] An authentication route is chosen for the public directories (OAuth on the endpoint, or an agreed API-key exception).
- [ ] Final icon and logo artwork is added (`.claude-plugin/icon.png`, plus `assets/` for Codex).
- [ ] Reviewer test account, test cases, and walkthrough video are prepared for the OpenAI submission.
- [ ] The submitting organization is the long-term owner (both directories bind a listing to the first organization that submits it).
