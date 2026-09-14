# Connector: StudioTwin Web MCP

**Status: live.** This page owns the runtime contract and operations only. For endpoint, authentication, client connection, and no-cost discovery, follow [Web MCP onboarding](../onboarding/web-mcp.md).

Web MCP is host-agnostic and editor-free. It can operate standalone and is also the StudioTwin cloud service required by the Blender connector.

## Runtime contract

For the connection contract, including transport and supported capabilities, follow [Web MCP onboarding](../onboarding/web-mcp.md).

- On initialization, the server identifies itself as StudioTwin and supplies baseline instructions.
- Generation tools are derived from the platform's public function registry. Discover the live set and read each definition immediately before use.
- Generation returns a job identifier asynchronously. Preserve it and poll the existing job rather than resubmitting.
- Outputs are asset references and presigned download URLs, not automatic Editor imports.

## Runtime operations

The live tool list is authoritative. Expected operation families include:

- estimate generation cost and read credit balance;
- submit generation and poll, list, or cancel owned jobs;
- list and resolve owned assets;
- create and complete an asset upload session; and
- download generated environment, material, mesh, motion, and audio outputs through returned asset data.

Do not hardcode tool names, schemas, costs, terminal-state spellings, or polling intervals from this guide.

## Workflow

1. Discover the live tools and inspect the selected definitions.
2. Estimate cost and confirm authorization before a credit-consuming operation.
3. For local inputs, create an upload session, transfer the file as directed, complete the upload, and preserve the completed asset identifier separately from the upload-session identifier.
4. Submit generation once and preserve every returned identifier by role.
5. Poll the original job until the live response reports a terminal state.
6. Resolve and download the authorized outputs, or pass supported asset references to another connected host workflow.
7. Redact API keys, upload credentials, cloud URIs, and presigned URLs from logs and reports.
8. Verify downloaded files or resolved assets rather than treating a terminal job state as the complete deliverable.

Motion generation and download are available through Web MCP. StudioTwin Blender motion import is **in progress**; do not attempt or improvise it until the live Blender addon exposes it.

## Blender delegation

For Blender, Web MCP owns every StudioTwin cloud action: generation schemas, uploads, cost estimates, credit balance, job submission/status/cancellation, asset listing/resolution, and output download. Supported Blender import operations through official Blender MCP and the StudioTwin Blender addon begin only after files exist at persistent local paths. Setup verification and authorized read-only Blender inspection may occur earlier; neither imports nor mutates an output. Follow [Blender onboarding](../onboarding/blender.md) and the [Blender runtime guide](blender-mcp.md).

## Errors and credits

- A low-balance response must be surfaced plainly. Use the live cost and balance operations, then route to [credits](../onboarding/credits.md).
- An ambiguous transport response does not authorize a second paid submission. Preserve any job or correlation identifier and recover the existing operation when possible.
- An expired presigned URL requires re-resolving the existing asset, not regenerating it.

The operating policy in `SKILL.md` still applies: plan from intent, inspect live definitions, submit once, disclose costs, verify artifacts, and never expose secrets.