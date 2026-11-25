# Video generation workflow

**Status: guidance.** This page sequences a StudioTwin video generation through [Remote MCP](connectors/remote-mcp.md): inspect the live definitions, plan around the output, prepare the colour or layout control source and one concept frame, upload or resolve inputs, estimate and authorize, then submit and deliver the MP4. It adds no MCP surface. The operating policy in [`SKILL.md`](../SKILL.md) and the job lifecycle in [operations](operations.md) still apply.

The live MCP definitions are the authority for function names, fields, defaults, accepted values, limits, costs, and output shape. Names and limits below help you choose a path. They are not a schema. If a live definition differs from this page, follow the live definition and see [troubleshooting](troubleshooting.md#a-tool-differs-from-this-guide). The one exception is a value that a definition lists but this page marks as unsupported by the current tool, such as depth, canny, or pose control on LTX-2.5. Do not use it.

StudioTwin video support for Unreal Engine and Blender is upcoming. This page does not cover capture, preparation, generation, or import inside either host.

## What a video generation needs

- **Colour or layout control source.** A colour, clay, or previs render of the shot that fixes motion, camera, and composition. Supply it as a control video or as a StudioTwin image-sequence asset, exactly one of the two. This primary source is always required. A depth, canny, or pose pass on its own is never a valid control source.
- **Supplementary depth pass, only where the tool uses it.** A depth pass of the same shot, alongside the colour frames, is recommended for some video tools because it gives more consistent structure. Of the current tools, only LTX-2.3 depth uses one, and only from an image-sequence asset that carries it. The depth pass comes in addition to the colour source, which stays required.
- **Concept frame.** One image that shows how a single moment of the clip should look. This is the `conceptFrame` input.
- **Frame index.** The timeline position that the concept frame represents. This is `conceptFrameIdx`.
- **Explicit frame count.** The `numFrames` input. It is required, it sets the cost, and it has no default.
- **Prompt.** The look, material, and motion instruction.

The current video definitions take one concept frame per generation. They expose no second frame, end frame, reference sheet, or image list, and no ingredients, multi-view input, 4K output, upscaling, or HDR video. Do not pass or offer these while the definitions do not expose them. Tool support can change, so read the live definitions each time for any input that is newly required, recommended, or supported, and use only what they expose.

## 1. Discover and inspect

1. Discover the live tools. Identify the video generation functions, the ConceptLab Concept tools, and the cost estimate, upload, job, and asset operations.
2. Read each candidate video definition in full. Note the required fields, the frame-count rule, the accepted sizes, the fps behavior, the control types, the inputs marked as recommended, and the output fields.
3. Read the ConceptLab type catalog and the type details for `frame`. They describe the Frame input roles, the canvas rules, and how each operation behaves.

If no video function is discovered, video generation is unavailable on this connection. Report that. Do not improvise video through another tool. Video can be available on one StudioTwin environment before another, so check the connection you are using.

**Plan around the output.** Before choosing a tool, write down what the user wants delivered: duration, fps, size or aspect ratio, and which control source, depth pass, and concept frame exist. When the user has already decided the duration or the size, choose the supported tool that delivers it. If no tool delivers it exactly, explain the mismatch and the closest supported options, and let the user choose. Do not silently change the duration, size, aspect ratio, or fps to fit a tool.

## 2. Choose the model

Two functions are currently expected. Confirm each name and envelope in the live definition before you use it:

| Need | Expected function | Control the current tool supports | Envelope to confirm live |
| --- | --- | --- | --- |
| Most work: layout-to-render, longer clips, a choice of sizes and aspect ratios | `ltx25-video` (LTX-2.5) | A colour, clay, or previs source with control type `raw`. It does not use a depth pass. | 9 to 721 frames. Width and height in multiples of 64, with presets in several aspect ratios. |
| A short clip with a depth pass of the same shot | `ltx23-video-depth` (LTX-2.3 depth) | Colour frames plus a supplementary depth pass in one image-sequence asset, with the default control type `depth`. | Exactly 896×512. 9 to 121 frames. |

- **Default to LTX-2.5.** Use control type `raw` with a colour, clay, or previs source. The current LTX-2.5 tool is a layout-to-render model. It does not support depth, canny, or pose control, even though its `controlType` field also lists those values. Do not select them. A depth pass in an image-sequence asset is not used by this tool.
- **Use LTX-2.3 depth only with a depth pass.** Choose it only when the shot fits its fixed size and frame range and an existing validated image-sequence asset carries both the colour frames and a depth pass. Without a depth pass, use LTX-2.5. This guide does not cover its canny or pose control types, so do not select them on your own.
- **Respect the envelope.** Requests outside a tool's envelope are rejected before dispatch. Do not submit them to probe the limit. If a shot needs more frames or another size than LTX-2.3 depth allows, offer LTX-2.5 and say that it will not use the depth pass.
- Do not split one shot into several paid generations without the user's agreement.

## 3. Prepare the control source

Decide the output size, fps, and frame count first, because the source must cover them.

- **Frame count.** `numFrames` must be a value the live definition accepts (currently `8k+1`, such as 9, 121, or 241). Derive it from the requested duration and fps. If the duration does not land on an accepted value, tell the user the nearest accepted durations and confirm one before you estimate. For example, about 5 seconds at 24 fps is 121 frames.
- **Coverage.** The source must contain at least `numFrames` frames. A shorter source is rejected before generation and is not padded. A longer source is trimmed to `numFrames`.
- **fps.** Leaving fps at its source-rate default uses the source's frame rate. When you set it, use one of the documented presets. Either way, read the resolved fps from the returned configuration and report it.
- **Size.** Pick the output width and height from the live definition. The video functions fit the control source to that size. Confirm the source's aspect ratio matches the output so that nothing important falls outside the frame. If it does not, report the mismatch rather than cropping silently.
- **Control type.** Use `raw` for a colour, clay, or previs source on LTX-2.5. On LTX-2.3 depth, keep the default `depth`, which applies to the depth pass in the image-sequence asset. The live definition describes the expected depth direction.

**Control video.** Upload the colour or layout video through the Remote MCP upload lifecycle, then confirm the completed asset resolves as a video. A control video carries no depth pass. The live definitions do not list accepted containers or codecs. If a file is rejected, report the error rather than converting it repeatedly.

**Image sequence.** The live definitions accept a StudioTwin `image_sequence` asset: a TAR archive that StudioTwin validates on upload completion, which requires the archive's ordered entries. It carries the colour frames and, optionally, a depth pass. The archive layout, frame naming, ordering, image format, depth-pass format, and metadata are not published in the live definitions or the public StudioTwin docs. Do not assemble one from guesswork. Use an existing validated `image_sequence` asset or a control video. Otherwise, tell the user that sequence packaging is not yet publicly specified.

## 4. Prepare the concept frame

The concept frame should match the colour or layout source at the frame index you will use.

1. **Pick the index.** Use `0`, the first frame, by default. It gives the closest adherence to the concept look. Choose a later index when the opening frames do not show the main elements: a slow reveal, or a starting point of view that misses the main character or primary object. Pick the first source frame where those elements are clearly visible, and set `conceptFrameIdx` to that frame's index. Tell the user why. On long LTX-2.5 clips, an index outside the first window can let the earlier frames drift from the concept look. The returned warnings say so, so report them.
2. **Take the matching source frame.** Export the colour or layout frame at that exact index from the control source, and upload it as an image. It becomes the layout input that fixes composition. Never use a depth, canny, or pose frame for this. If the user has only a depth, canny, or pose pass, stop and ask for the colour or layout render of the shot. It is required as the control source too.
3. **Create or edit a ConceptLab `frame` Concept.** Bind the uploaded layout frame as the Frame's previs input, and optionally bind **at most one** style reference image. Then generate with an explicit size and layout kind, following the live `frame` type details. Inputs must be image assets you own. Bindings are revisioned, so pass the current revision the live details require.
4. **Match the shape.** The concept frame's aspect ratio must match the video's width and height closely (currently within 1%). StudioTwin resizes the frame to the video size before conditioning. The Frame canvas has its own limits, so the frame may not be able to use the exact video size. 896×512, for example, is below the Frame minimum, so use a same-aspect canvas such as 1792×1024. Never resize, crop, or pad an image silently to make it fit.
5. **Review before paying for video.** Suggest that the user reviews the frame in ConceptLab. For a targeted change, use the live Frame edit operation after the user says what should change. The `frame` Concept type takes no masks or regional prompts. Each generate or edit is a separate paid call.

**Already-prepared image.** If the user supplies an approved image, you can upload it and use that asset as the concept frame instead of creating a Concept. It must match the source at the chosen index and meet the same aspect-ratio rule. Explain that it carries no Concept lifecycle, history, or ConceptLab review.

**Concept versus image asset.** `conceptFrame` takes an image **asset**, not a Concept id. After the Frame Concept is approved, read the Concept and pass its **active output asset**. Do not pass its previs or style input, and do not pass the Concept id. Video inputs must be assets you own. If the approved frame belongs to another member's Concept, use the live ConceptLab reference operation to copy it into an asset you own. A standalone frame-restyle function may also be discovered. It returns only an image asset, with no Concept lifecycle. Use it only when the user wants a one-off image.

## 5. Upload or resolve the inputs

Follow the Remote MCP [workflow](connectors/remote-mcp.md#workflow) for uploads. Keep the completed asset id separate from the upload-session id. Reuse assets that already exist rather than uploading them again. Pass each input in the reference form the live definition names: the control-sequence field documents `vpc::assets/<uuid>`. Before you estimate, resolve each input and confirm that its type, size, and frame count match the plan.

## 6. Estimate and authorize

Run the live cost estimate with the exact inputs you will submit, including `numFrames`, size, control type, and the concept frame. Video cost scales with the frame count. If the estimate rejects the inputs, correct them. Do not submit to find out. State the estimate and get authorization for that one generation. If no estimate is available, say the cost is unknown before asking, as `SKILL.md` requires.

## 7. Submit, poll, download, and verify

Submit once and follow the [asynchronous generation](operations.md#asynchronous-generation) and [download](operations.md#downloading-remote-outputs) lifecycle. Then check the video-specific results:

- Read the returned configuration and warnings. Confirm the delivered width and height, fps, frame count, control type, and concept frame index. On LTX-2.5, the faster preview quality delivers half the requested width and height. The configuration reports both values.
- Resolve and download the output video asset. If a download link expires, re-resolve the asset. Do not regenerate it.
- Verify the file. It exists, is non-empty, and has the expected frame count, duration, and dimensions. Use a local probe such as `ffprobe` when one is available. Do not expect an audio track.

For a browser project, treat the downloaded MP4 as a project file, as the [three.js destination guidance](destinations/threejs.md#boundaries) describes.

## Report

Include the items `SKILL.md` requires, plus:

- the video function used, and why, including any mismatch between the requested and delivered output;
- the Frame Concept id and the active asset id passed as the concept frame, or the uploaded image asset id, and the frame index and why it was chosen;
- the control source asset id and type, and whether a depth pass was used;
- the frame count, size, fps, and the estimated and charged cost where available;
- the job id, output asset id, and local MP4 path;
- what was verified in the file, and any warnings or mismatches.
