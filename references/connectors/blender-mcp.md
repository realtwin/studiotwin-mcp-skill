# Connector: Blender MCP

**Status: in development, not yet public.** The StudioTwin Blender extension
and hosted StudioTwin MCP launch together. Until StudioTwin publishes them, use
this workflow only when the operator has been given both components; do not
invent a download or production endpoint.

## What it is

StudioTwin reaches Blender through four cooperating parts:

| Component | Responsibility |
| --- | --- |
| Hosted StudioTwin MCP | Advertises generation schemas, accepts uploads, estimates cost, submits and reports jobs, and resolves assets. |
| Agent | Orchestrates the live tools and securely transfers files between the hosted service and the local machine. |
| Official Blender MCP | Runs Python in the open Blender process through `execute_blender_code`. |
| StudioTwin Blender extension | Applies supported local files to the Blender scene through deterministic importer functions. |

The StudioTwin extension is **not** an MCP server. It performs no network
requests, stores no API key, and does not manage generation jobs. It accepts
local files only. The hosted StudioTwin MCP cannot read Blender's local paths,
so local generation inputs must first be uploaded and generated outputs must be
downloaded before the Blender import stage.

```text
User intent
  -> hosted StudioTwin MCP (upload / generate / poll / resolve)
  -> local downloaded files
  -> official Blender MCP (`execute_blender_code`)
  -> StudioTwin importer
  -> Blender datablocks
```

Full installation and first-connection instructions:
[../onboarding/plugins.md](../onboarding/plugins.md#blender-in-development).
Hosted MCP transport and asset lifecycle: [web-mcp.md](web-mcp.md).
If discovery, connection, download, or import fails, read the Blender sections
in [../troubleshooting.md](../troubleshooting.md).

## Capability groups (toolkits)

Stable orientation only — discover the live tools at runtime. These capability
families belong to the hosted StudioTwin MCP; the Blender column describes what
the current importer extension can consume after the output is downloaded.

| Toolkit | What it does (image/text → asset) | Blender handling | Docs |
| --- | --- | --- | --- |
| Motion | Human animation from text or trajectory; edit, stitch, and retarget | No Blender importer; preserve and report the generated outputs | https://docs.studiotwin.ai/docs/plugin/toolkits/motion-toolkit/ |
| Environment | HDR environment maps from text or image; upscale, outpaint, and derive worlds | Apply supported `.hdr` or `.png` environment outputs; select other outputs by their actual format | https://docs.studiotwin.ai/docs/plugin/toolkits/environment-toolkit/ |
| Mesh | 3D mesh from an image reference | Import supported `.glb` outputs | https://docs.studiotwin.ai/docs/plugin/toolkits/mesh-toolkit/ |
| Material | PBR material from texture, image, or text references | Build a new unassigned material from supported PBR texture maps | https://docs.studiotwin.ai/docs/plugin/toolkits/material-toolkit/ |
| Audio | Sound effects from a text brief | Add supported audio outputs to the Blender sequencer | https://docs.studiotwin.ai/docs/plugin/toolkits/audio-toolkit/ |

Generation, downloading, Blender import, material assignment, scene placement,
and rendering are separate stages. A request for one does not authorize the
others.

## Route by requested outcome

- **Setup only:** install and verify each component without starting a paid
  generation.
- **Generation only:** use StudioTwin MCP; do not connect to or inspect Blender.
- **Import an existing StudioTwin asset:** resolve the asset, download the
  supported output files, then use Blender MCP to call the matching importer.
- **Generate and import:** complete and verify generation first, then download
  and import the supported result.
- **Ordinary Blender work:** this connector does not apply unless the request
  involves StudioTwin generation or StudioTwin assets.

Motion and animation may be generated and downloaded through StudioTwin, but
the Blender extension has no motion importer. Do not improvise one through
general Blender Python under this connector.

## Start every Blender-affecting session

Before the first Blender change:

1. Confirm the official Blender MCP reaches the intended open, interactive
   Blender process. Do not use `execute_blender_code_for_cli` for an open-scene
   import.
2. Inspect the current scene and relevant target objects or collections. Never
   assume the default empty scene.
3. Confirm `bpy.app.background` is false.
4. Discover the enabled StudioTwin extension module. Installed Blender
   extensions are commonly namespaced; do not assume that bare
   `import studiotwin` resolves the installed extension.
5. Confirm Blender and the agent can read the same absolute local paths.

Do not perform these Blender checks for a generation-only request.

## Operating workflow

1. **Discover the hosted tools.** Read the live StudioTwin definitions for the
   requested generation, cost, upload, job-status, and asset-resolution
   operations. Do not copy generation names or schemas from this guide.
2. **Prepare inputs.** Classify each supplied value as a scalar, local file or
   attachment, existing StudioTwin asset, or external URL. Never pass a local
   path to the hosted MCP. Upload local inputs through the live upload lifecycle
   and use the asset reference form required by the target generation schema.
   Keep the upload-session UUID separate from the completed asset UUID. When the
   live schema requires an owned-asset reference, use its canonical
   `vpc::assets/<asset-uuid>` form; never substitute a raw S3 URI, presigned URL,
   session UUID, or asset object.
3. **Estimate and authorize.** Use the live cost information or estimation
   capability before a credit-consuming call. When an estimator accepts a
   `functionName`, pass the exact platform operation identifier returned by the
   live generation definition. Do not convert it to snake case or substitute an
   MCP wrapper name. Generation authorization does not authorize Blender changes.
4. **Generate once.** Submit once and preserve every returned identifier. Label
   the submission `jobId` separately from any status, generation, or output
   `uuid`. Poll the existing job until the live response reports a terminal
   state; do not hard-code only `COMPLETE` or another spelling. Follow
   [../operations.md](../operations.md).
5. **Resolve and download.** Enumerate and preserve every distinct job output.
   Parse status responses internally and redact URL/URI fields before reporting
   or logging. Prefer a connector-native download operation that accepts an
   asset or job identifier. If a shell transfer is unavoidable, use a hidden
   non-echo path and do not pass presigned URLs through a visible PTY. Store files
   at persistent absolute paths visible to Blender.
6. **Select the importer.** Choose from the actual output format and meaning,
   not merely the generation operation's name. Import only when the user's
   request includes a Blender scene change.
7. **Call one importer.** Use the official Blender MCP
   `execute_blender_code` tool, discover the enabled extension namespace, and
   invoke exactly one StudioTwin importer in that call. Assign its
   JSON-serializable return value to the global `result` variable.
8. **Verify structurally.** Read the importer result and confirm the returned
   Blender datablocks exist. Render or perform visual inspection only when the
   user requested it.
9. **Report.** Include the job id when applicable, downloaded file paths,
   importer used, created or changed datablocks, warnings, unsupported outputs,
   and whether the Blender scene now has unsaved changes.

Never repeat paid generation merely because download or Blender import failed.
Resolve the existing asset again for an expired URL, or retry only the local
import after correcting the specific failure.

## StudioTwin importer contract

| Function | Accepted local input | Effect |
| --- | --- | --- |
| `apply_environment_map(filepath)` | `.hdr`, `.png` | Reuses or creates the active World, applies the environment, and switches open 3D viewports to Rendered shading. |
| `import_model(filepath)` | `.glb` | Imports the GLB and returns importer-reported object and mesh entries; verify actual object data-block names separately. |
| `import_material(map_paths, name=None, displacement_scale=0.1)` | `.hdr`, `.jpeg`, `.jpg`, `.png`, `.tif`, `.tiff` texture maps | Creates a new, unassigned Blender material. |
| `import_audio(filepath, frame_start=None, channel=None, name=None)` | `.aif`, `.aiff`, `.flac`, `.mp3`, `.ogg`, `.wav` | Adds a sound strip to the current scene's sequencer. |

Material map keys are exactly `albedo`, `heightmap`, `normals`, `roughness`,
and `metalness`. Omit unavailable maps. Do not silently substitute other names.

Import the enabled extension module before the operation:

```python
import bpy
import importlib

module_name = next(
    (
        addon.module
        for addon in bpy.context.preferences.addons
        if getattr(addon, "module", None)
        and (
            addon.module == "studiotwin"
            or addon.module.endswith(".studiotwin")
        )
    ),
    None,
)
if module_name is None:
    raise RuntimeError("The StudioTwin Blender extension is not enabled")

studiotwin = importlib.import_module(module_name)
```

Append exactly one importer call and assign its return value to `result`. Escape
file paths and user-provided names as data; never interpolate untrusted text as
executable Python. Treat importer-reported object and mesh entries as hints:
re-query the created object and its `obj.data.name` before reporting actual
datablock names.

## Action boundary

Importing authorizes the documented effects of the selected importer. It does
not authorize moving or scaling objects, assigning a newly created material,
changing the camera, saving the `.blend`, rendering, creating derivatives, or
making unrelated scene or UI changes. Perform only read-only structural checks
after import unless the user requested additional work.

The environment importer itself switches open 3D viewports to Rendered shading;
report that side effect. Do not make further viewport changes without an
explicit request.

Never expose API keys, upload credentials, or presigned download URLs.

## Authorities

- Official Blender MCP project: https://projects.blender.org/lab/blender_mcp
- Official Blender MCP guide: https://www.blender.org/lab/mcp-server/
- Blender extension management: https://docs.blender.org/manual/en/latest/editors/preferences/extensions.html
