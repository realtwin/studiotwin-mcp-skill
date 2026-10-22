# Video generation workflow

**Status: guidance.** This page sequences a StudioTwin video generation: choose a model, prepare one concept frame, submit, and deliver the MP4. It adds no MCP surface. Video generation and its Concept frame step run through [Web MCP](connectors/web-mcp.md). The operating policy in [`SKILL.md`](../SKILL.md) still applies.

The live MCP definition is the authority for function names, input fields, defaults, accepted values, limits, costs, and output shape. Names and limits below are orientation for choosing a path, not a schema. If a live definition differs from this page, follow the live definition and see [troubleshooting](troubleshooting.md#a-tool-differs-from-this-guide).

## What a video generation is

A video generation turns a control source into an MP4 that follows a text prompt and one approved concept frame:

- **Control source:** a layout, depth, canny, pose, or raw video, or an image sequence, that fixes motion and composition.
- **Concept frame:** a single image showing how one moment of the clip should look. It is usually the active asset of a Concept of type `frame`, created in ConceptLab or through the Concept tools.
- **Prompt:** the look, materials, and motion intent.

Each generation accepts one concept frame and its frame index. Do not try to pass a second frame, an end frame, or an array of frames. Do not describe ingredient sheets, multi-view concepts, 4K output, upscaling, or HDR output as video capabilities unless a live definition exposes them.

## 1. Discover the live schema

1. Discover the live tools. Find the video generation functions, the Concept tools, and the estimate, upload, job, and asset operations.
2. Read the full definition of each video function you might use. Note which fields are required, how each field is described, and the accepted values for frame count, size, and frame rate.
3. Read the Concept tool definitions and confirm that `frame` is an accepted Concept type. If no `frame` type is available, do not substitute a different Concept type. Tell the user, then either use an approved image the user provides or stop.

If no video function is discovered, video generation is unavailable on this surface. Do not improvise it through another tool.

## 2. Choose the model

The current video functions are expected to be discovered as follows. Confirm both the name and the envelope in the live definition:

| Need | Expected function | Envelope to confirm live |
| --- | --- | --- |
| Most work: longer clips, layout-to-render, a choice of sizes and aspect ratios | `ltx25-video` (LTX-2.5) | Up to 721 frames; size presets in several aspect ratios |
| Short clip driven tightly by a depth, canny, or pose pass | `ltx23-video-depth` (LTX-2.3 depth) | 896×512 only, up to 121 frames |

Rules of thumb:

- Default to LTX-2.5. It is the longform model, and it accepts raw layout video as well as depth, canny, or pose control.
- Choose depth only when the user has, or wants, a depth-style control pass and the shot fits 896×512 and 121 frames. A depth field may accept a wider range than the model supports. Shapes or counts outside the supported envelope fail before dispatch, so do not submit them to probe the limit.
- If the user needs a longer clip, a different size, or a different aspect ratio than depth supports, use LTX-2.5. Do not split a long shot into several depth calls without the user's agreement, because each call is a separate paid generation.

## 3. Create or edit the concept frame

The concept frame should be a Concept of type `frame`. A Concept keeps the frame's lifecycle and active asset, so you can revise it and reuse the same identity; see [Concepts](capabilities.md#concepts). Follow the live Concept tool definitions for every field.

1. **Pick the anchor moment.** Recommended: index `0`, the first frame of the clip (see [Anchor recommendation](#anchor-recommendation)).
2. **Prepare the layout source.** Use the control source's frame at that index, for example the first frame extracted from the layout video, so the concept frame matches the control composition. Upload a local file through the live upload lifecycle and keep the completed asset id.
3. **Optionally add one style reference.** A `frame` Concept takes one layout source and at most one style reference image. Do not pass several references.
4. **Match the size to the video.** Request a frame whose width and height equal the intended video width and height, in the size form the live definition accepts. If the returned frame is not that exact size, do not resize or crop it silently. Report the mismatch.
5. **Generate, then review.** Estimate cost, confirm authorization, then create and generate the Concept. Suggest that the user review the frame visually in ConceptLab before spending credits on video.
6. **Edit instead of regenerating.** For a targeted fix, use the live Concept edit operation on the active frame, with a mask where the definition supports one. Each edit is a new paid call. Edit only after the user has said what should change.

Use the frame Concept's active asset as the concept frame for the video. If the user supplies an approved image instead, upload it and use that asset, and explain that it will not carry Concept lifecycle state.

## 4. Prepare and estimate the video request

Fill the inputs from the live definition. These fields shape this workflow:

- **`conceptFrame`:** the single approved frame asset from step 3.
- **`conceptFrameIdx`:** the timeline index that the frame represents. Recommended `0`.
- **`numFrames`:** always set this to an explicit value that the live definition accepts. Do not rely on a default, an omitted value, or the source length. The frame count sets the cost, so derive it from the user's requested duration and frame rate, then confirm that it is a value the definition accepts.
- **Control source:** a control video or an image sequence, as the definition allows, and the matching control type.

Check the control source before you submit. It must contain at least `numFrames` frames at the chosen rate. A source that is too short is rejected rather than padded. A longer source is trimmed to the requested count. Use the live definition's rules for width, height, and fps.

Run the live cost estimate with the exact inputs you will submit. State the cost and get authorization. If no estimate is available, say the cost is unknown before asking for authorization, as `SKILL.md` requires.

### Anchor recommendation

Recommend index `0` and start-frame-only conditioning. In StudioTwin's internal evaluation, a single start frame produced cleaner clips than adding an end anchor: end anchors caused morphing and crossfades within a window. This is why the video functions accept one concept frame.

The live definition may accept other indexes. Use a later index only when the user needs a specific moment to match the frame, for example a reveal mid-shot, and explain that index `0` is the recommended path.

## 5. Submit, poll, resolve, download

Follow [operations](operations.md#asynchronous-generation):

1. Submit exactly once, with the estimated inputs.
2. Preserve every returned identifier by role, such as the submission job id and any output or asset uuid.
3. Poll the original job at the interval recommended by the live response. Long clips can take a while. Do not resubmit because a job is still running or a transport response was ambiguous.
4. On success, inspect warnings and the returned resolved configuration. Confirm that the frame count, size, fps, and concept frame index match the request.
5. Resolve the output video asset and download it through the connector-native download operation. Redact presigned URLs, as [operations](operations.md#downloading-remote-outputs) describes. If a URL has expired, re-resolve the asset; do not regenerate.
6. Verify the downloaded MP4: the file exists, is non-empty, and has the expected duration, frame count, and dimensions. Use a local probe such as `ffprobe` when one is available. Do not expect an audio track.

## Host destinations

- **Web / no DCC:** the deliverable is the resolved video asset and the downloaded MP4. Stop there unless the user asks for more.
- **Blender:** Web MCP generates and downloads the video. The StudioTwin Blender addon has no video importer. Report the local MP4 path and asset id. Do not improvise a sequencer or texture import unless the user asks for ordinary Blender work, which is outside this skill.
- **Unreal Engine:** the StudioTwin UE plugin toolkits do not include video generation. If the Unreal MCP surface shows no video tool, use Web MCP for the generation when it is connected and the user agrees, then report the downloaded MP4. Do not claim a StudioTwin Unreal video import.
- **three.js / browser:** treat the MP4 as a project file, as the [three.js destination guidance](destinations/threejs.md#boundaries) describes. Never embed a presigned URL.

## Report

Include the items `SKILL.md` requires, plus:

- the model and function used, and why;
- the concept frame's Concept id and active asset id, and the frame index used;
- the explicit frame count, size, fps, and the estimated and charged cost where available;
- the job id, output asset id, and local MP4 path;
- what was verified in the file, and any warnings or mismatches.
