# Troubleshooting

## No StudioTwin tools appear in Unreal

1. Confirm the correct Unreal project is open.
2. Confirm the StudioTwin plugin is installed, enabled, and compatible with the Editor version.
3. Confirm the MCP toolset-registry dependency is installed and enabled.
4. Check the Unreal Output Log for module-load or registration errors.
5. Confirm the MCP client uses the endpoint and transport from the installed connector release.
6. Restart the Editor after plugin changes.
7. Reconnect and repeat live discovery.

Do not guess missing server commands, ports, or package locations.

## Hosted StudioTwin MCP is missing for Blender

If official Blender MCP tools are present but no hosted StudioTwin tools appear,
the local Blender bridge is connected but cloud generation is not configured.

1. Confirm the operator has access to the not-yet-public hosted StudioTwin MCP.
2. Confirm its server entry is present in the active MCP client's configuration.
3. Confirm the API key is active and supplied through the client's secret or
   environment configuration, never through chat or Blender.
4. Restart or reconnect the MCP client after configuration changes, then repeat
   live tool discovery.

Do not treat Blender MCP tools as StudioTwin generation tools, and do not invent
the hosted endpoint when StudioTwin has not supplied it. Follow the Blender
setup sequence in [onboarding/plugins.md](onboarding/plugins.md#blender-in-development).

## Official Blender MCP does not connect

1. Confirm Blender 5.1 or newer is running with the intended `.blend` open.
2. Confirm the Blender Lab **MCP** extension is installed and enabled.
3. Confirm Blender's **Allow Online Access** preference is enabled.
4. In the MCP extension preferences, confirm the bridge reports
   **Server is running**; start it manually if Auto Start did not succeed.
5. Confirm the Blender bridge and MCP client use the same host and port. The
   documented defaults are `localhost:9876`; custom values must match
   `BLENDER_MCP_HOST` and `BLENDER_MCP_PORT` in the client configuration.
6. Check whether another Blender process already owns the configured port. Stop
   or reconfigure the unintended process before reconnecting.

Do not change ports speculatively. Read the installed extension preferences and
client configuration first.

## Blender MCP reaches the wrong process

Before changing an open scene, use `execute_blender_code` to read
`bpy.app.background`, `bpy.data.filepath`, and the Blender version. If
`background` is true or the filepath is not the intended open file, stop. A
background or different Blender process may own the configured bridge port.
Correct the running server or port mapping and reconnect; do not import into the
wrong process.

## StudioTwin Blender extension is missing

If Blender MCP connects but the StudioTwin module cannot be found:

1. In Blender Preferences, confirm **StudioTwin** is installed and enabled.
2. Inspect addon records, using their `module` field (the collection yields
   addon objects, not module-name strings):
   ```python
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
   ```
3. Import that discovered module name with `importlib`; do not assume a bare
   `import studiotwin` refers to the installed extension.
4. Save Blender preferences and, after restarting Blender, verify the
   extension remains enabled; an installed directory alone is not proof.
5. Confirm `apply_environment_map`, `import_model`, `import_material`, and
   `import_audio` are callable.

If a function is absent, report the installed extension as incompatible with
this guide and request the matching build. Do not replace the importer with
ad-hoc Blender Python.

## Blender cannot read a downloaded output

Confirm the output was downloaded to a persistent absolute path visible to both
the agent and Blender. Check that the file still exists and that its extension
is supported by the selected importer. Do not pass a presigned URL, cloud asset
reference, or agent-only temporary path to a local Blender importer.

If a presigned URL expired before download, resolve the existing asset again.
Do not rerun paid generation solely to obtain a fresh URL.

## Presigned URL exposure or transfer failure

Never forward raw status or download output that contains signed URLs or cloud
URIs. Parse those fields internally, redact them from transcripts and reports,
and prefer a connector-native downloader. If a shell transfer is unavoidable,
use a hidden, non-echo path and report only the sanitized local destination.
An expired URL should trigger re-resolution of the existing asset, not paid
regeneration.

## Blender import is partial or fails

Preserve the importer result or traceback and verify the selected file belongs
to the completed job. Correct only the failed local stage; do not resubmit the
generation. After a successful importer return, confirm the reported Blender
datablocks exist and disclose missing objects, maps, strips, or other partial
results. For model imports, re-query the actual object and mesh datablock names
(for example, `obj.data.name`) instead of assuming a returned mesh label is the
datablock name.

The Blender extension supports environment maps, GLB models, PBR texture maps,
and audio. It has no motion importer. Preserve unsupported outputs and report
the limitation rather than improvising a conversion.

## A tool differs from this guide

Follow the live tool definition. This skill intentionally contains operating guidance, not duplicated schemas. Plugin versions may add, remove, or change tools and constraints.
For cost estimation, pass the exact platform operation identifier from the live
generation definition (for example, a hyphenated ID); never derive a
snake-case name from a wrapper or tool name. Treat the live terminal vocabulary
as authoritative (`done`, `complete`, and other values may be valid).
When responses contain multiple identifiers, label submission `jobId` separately
from status, generation, or output `uuid` values.

## Submission response is ambiguous

Do not automatically submit again. Preserve the response and transport logs, look for a job or correlation identifier, reconnect if needed, and poll the original job when possible. Ask before making a second paid attempt.

## Job remains running

Honor the live retry hint or polling interval. Do not treat the interval as a completion estimate. Preserve the identifier and report the current state without promising a finish time.

## Job failed

Capture the identifier, error text, relevant warnings, and sanitized inputs. Check live validation requirements and source accessibility. Correct the specific issue before proposing another paid attempt.
If the terminal response contains only `failed` without an error code,
message, or provider detail, preserve that opaque failure: record the original
submission job ID, any distinct status/output UUIDs, timestamps, and empty or
partial outputs. Do not infer the provider cause.

## Job succeeded but assets are missing

Treat this as partial success. Inspect returned notes, object paths, expected roles, import destinations, and Unreal logs. Verify whether some assets imported successfully. Do not rerun generation when only the import stage needs diagnosis.

## Paths are rejected

Determine which reference type the live definition accepts: UE object path, local filesystem path, URI, or another connector-specific reference. Do not substitute a Web/cloud reference for a UE path unless explicitly supported.

## Level or sequence changed unexpectedly

Stop further mutations. Identify created actors or assets and the current dirty state. Do not delete, overwrite, undo, save, or roll back without explicit authorization and an understood recovery path.

## Cost or runtime is unclear

State that it is unknown. Do not import figures from the Web MCP or historical documentation. Use only information exposed by the live UE connector or an authoritative, version-matched policy.
