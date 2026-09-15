# Capability selection

Use this guide to map user intent to live StudioTwin capabilities. The categories are stable orientation, not a tool catalog. Discover the current tool names and definitions at runtime, then follow the selected host connector for its import and mutation contract:

- [Unreal Engine](connectors/ue-mcp.md)
- [Blender](connectors/blender-mcp.md)
- [Web / no DCC](connectors/web-mcp.md)

## Capability families

### Audio

Use for generating sound effects from a textual brief. Clarify duration, style, intensity, looping needs, and intended scene role when relevant. Unreal import follows the live UE tools. The Blender importer accepts only `.aif`, `.aiff`, `.flac`, `.mp3`, `.ogg`, and `.wav` and adds a sound strip to the sequencer.

### Environments and worlds

Use for generating environments from text or images, expanding or increasing environment-map resolution, and deriving world data or geometry from an environment. Unreal may also place resulting world content into the current level. The Blender importer applies only `.hdr` and `.png` environment maps.

Treat generation, world derivation, download, import, and host-scene placement as separate stages. Confirm before any stage that mutates an open level or Blender scene.

### Materials

Use for deriving PBR texture sets from source textures, images, or text. Unreal can create material assets or instances through its live tools. Blender can create a new, unassigned material from supported `.hdr`, `.jpeg`, `.jpg`, `.png`, `.tif`, and `.tiff` maps with the exact keys `albedo`, `heightmap`, `normals`, `roughness`, and `metalness`.

Verify which maps and host assets were actually produced. Do not assume every advertised material role exists when an import completes.

### Meshes

Use for image-driven 3D generation and import. The Blender importer accepts only `.glb`; Unreal import follows the live UE tools.

If the selected StudioTwin connector does not provide a tool to generate the required source image, obtain one separately. For Unreal, use this sequence:

1. **User:** Provide the source image. OR **Agent:** Generate an image with any other tool based on user's description.
2. **User or agent:** Import the image into the Unreal project using an available Unreal import workflow.
3. **Agent:** Verify the imported image asset and its UE object path.
4. **Agent:** Use that project asset as the source for the discovered StudioTwin mesh tool.

For hosted generation, upload a local source image through the live upload lifecycle rather than passing its path to the service. After generation and any authorized import, verify UE object paths and classes or Blender object and mesh datablocks, plus materials, textures, scale, orientation, and missing roles as applicable.

### Motion and animation

Use for trajectory-driven or text-driven motion generation, motion modification or stitching, importing animation data, retargeting where supported, and creating trajectory-control sequences in Unreal. Web MCP supports motion generation and download. StudioTwin Blender motion import is **in progress**; preserve and report generated motion outputs, and do not attempt or improvise Blender import until the live addon exposes it.

Motion workflows may impose strict frame-rate, frame-span, range, skeleton, and retargeting constraints. Read the live definitions and runtime validation rather than maintaining a second static contract here.

### Job status

Use the discovered polling capability for asynchronous operations. Poll the identifier returned by the original submission; do not start a second generation to check progress.

## Selection rules

1. Prefer the capability that directly produces the requested host deliverable.
2. Reuse an existing acceptable asset instead of generating a replacement.
3. Separate paid generation from free/local processing.
4. Separate content creation from placement, sequence creation, saving, and rendering.
5. Chain stages only when the earlier output is verified.
6. Ask for clarification only when the missing choice materially changes cost, target assets, mutation scope, or creative direction.
