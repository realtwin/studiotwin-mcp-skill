# Troubleshooting

## No StudioTwin tools appear

1. Confirm the correct Unreal project is open.
2. Confirm the StudioTwin plugin is installed, enabled, and compatible with the Editor version.
3. Confirm the MCP toolset-registry dependency is installed and enabled.
4. Check the Unreal Output Log for module-load or registration errors.
5. Confirm the MCP client uses the endpoint and transport from the installed connector release.
6. Restart the Editor after plugin changes.
7. Reconnect and repeat live discovery.

Do not guess missing server commands, ports, or package locations.

## A tool differs from this guide

Follow the live tool definition. This skill intentionally contains operating guidance, not duplicated schemas. Plugin versions may add, remove, or change tools and constraints.

## Submission response is ambiguous

Do not automatically submit again. Preserve the response and transport logs, look for a job or correlation identifier, reconnect if needed, and poll the original job when possible. Ask before making a second paid attempt.

## Job remains running

Honor the live retry hint or polling interval. Do not treat the interval as a completion estimate. Preserve the identifier and report the current state without promising a finish time.

## Job failed

Capture the identifier, error text, relevant warnings, and sanitized inputs. Check live validation requirements and source accessibility. Correct the specific issue before proposing another paid attempt.

## Job succeeded but assets are missing

Treat this as partial success. Inspect returned notes, object paths, expected roles, import destinations, and Unreal logs. Verify whether some assets imported successfully. Do not rerun generation when only the import stage needs diagnosis.

## Paths are rejected

Determine which reference type the live definition accepts: UE object path, local filesystem path, URI, or another connector-specific reference. Do not substitute a Web/cloud reference for a UE path unless explicitly supported.

## Level or sequence changed unexpectedly

Stop further mutations. Identify created actors or assets and the current dirty state. Do not delete, overwrite, undo, save, or roll back without explicit authorization and an understood recovery path.

## Cost or runtime is unclear

State that it is unknown. Do not import figures from the Web MCP or historical documentation. Use only information exposed by the live UE connector or an authoritative, version-matched policy.
