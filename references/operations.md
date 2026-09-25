# Jobs, imports, and Editor mutations

## Asynchronous generation

Most StudioTwin generation workflows are asynchronous.

1. Inspect the live submission definition.
2. Confirm inputs and authorization.
3. Submit exactly once.
4. Capture every returned identifier verbatim. If the response contains a
   submission `jobId` and a separate status, generation, or output `uuid`, label
   and preserve them separately.
5. Poll the existing job at the interval recommended by the live response or definition.
6. While running, wait rather than resubmit.
7. On success, inspect warnings, notes, and imported-artifact metadata.
8. On failure, preserve the error and job identifier for diagnosis.

A timeout, disconnect, or malformed transport response does not prove that submission failed. Attempt to recover the original submission identifier or inspect status before considering another paid call.

Treat the live status vocabulary as authoritative. A successful terminal state
may be reported as `done`, `complete`, or another connector-specific value; do
not hard-code one capitalization or spelling. Preserve opaque failures exactly
when the service supplies no diagnostic code or message, and do not infer a
provider cause.

A job that ends in a failed state with an empty error, or remains queued or
running for more than 1.5 hours, may indicate input rejection. Re-read
the live schema and validate the original input before considering another
submission. Never resubmit unchanged.

Do not promise a runtime unless the live tool or service supplies one. A polling interval is not an estimated completion time.

## Downloading remote outputs

Job-status responses may contain presigned URLs. Parse them internally and
redact URL and URI fields before emitting logs, forwarding tool results, or
reporting status. Prefer a connector-native download operation that accepts a
job or asset identifier. If a shell transfer is unavoidable, use a hidden
non-echo path and never pass the signed URL through visible PTY input or output.
An expired URL should trigger asset resolution again, not paid regeneration.

## Imported content

Generation may both produce remote content and import it into Unreal. StudioTwin downloads generated source files—such as images, GLB files, and other intermediate deliverables—to `<ProjectRoot>/.studiotwin/` before or during import.

After completion, verify:

- expected UE object paths exist;
- asset classes and roles match the request;
- required source files resolved;
- materials, textures, animation, or related assets are present;
- warnings or missing roles are disclosed;
- destination and naming are acceptable.

An import can be partially successful. Do not hide missing components behind a general success status.

If generation succeeds but Unreal import fails, check `<ProjectRoot>/.studiotwin/` for the source file associated with the job. When the source is complete and matches the job, retry only the import stage instead of rerunning paid generation. Do not import unrelated or stale files.

## StudioTwin changes to the Unreal project

Some StudioTwin workflows do more than generate remote content: they import assets, place generated world content, load animation, or create sequences in the open Unreal project.

Before using one of these StudioTwin capabilities, identify the intended destination and confirm that the requested project or level change is in scope. Afterward, verify the created asset, actor, animation, or sequence.

This guidance applies to changes made by StudioTwin workflows. General Unreal editing through optional UE toolsets is outside this skill.

## Motion-specific preparation

Validate required frame rate, frame count or span, trajectory ranges, skeleton compatibility, and retargeting inputs against the live definition. A known workflow may require 30 fps, but the live definition and runtime validation remain authoritative.

## Provenance record

Keep a concise execution record containing:

- discovered operation used;
- submission job id and any distinct status, generation, or output identifiers;
- source UE paths, local paths, or URIs;
- output UE object paths and roles;
- mutations made;
- warnings, partial results, and verification status.

Exclude credentials and transient signed material.
