# Capability selection

Use this guide to map user intent to live UE MCP capabilities. The categories are stable orientation, not a tool catalog. Discover the current tool names and definitions at runtime.

## Capability families

### Audio

Use for generating and importing sound effects from a textual brief. Clarify duration, style, intensity, looping needs, and intended scene role when relevant.

### Environments and worlds

Use for generating environments from text or images, expanding or increasing environment-map resolution, deriving world data or geometry from an environment, and placing resulting world content into the current level.

Treat generation, world derivation, and level placement as separate stages. Confirm before the placement stage because it mutates the open level.

### Materials

Use for deriving PBR texture sets from source textures, images, or text and for creating Unreal material assets or instances from the results.

Verify which maps and UE assets were actually produced. Do not assume every advertised material role exists when an import completes.

### Meshes

Use for image-driven 3D generation and import.

If StudioTwin UE MCP does not provide a tool to generate the source image required by this workflow, suggest the following alternative steps:

1. **User:** Provide the source image. OR **Agent:** Generate an image with any other tool based on user's description.
2. **User or agent:** Import the image into the Unreal project using an available Unreal import workflow.
3. **Agent:** Verify the imported image asset and its UE object path.
4. **Agent:** Use that project asset as the source for the discovered StudioTwin mesh tool.

After generation, verify imported object paths, mesh class, materials, textures, scale, orientation, and missing roles.

### Motion and animation

Use for trajectory-driven or text-driven motion generation, motion modification or stitching, importing animation data, retargeting where supported, and creating trajectory-control sequences.

Motion workflows may impose strict frame-rate, frame-span, range, skeleton, and retargeting constraints. Read the live definitions and runtime validation rather than maintaining a second static contract here.

### Job status

Use the discovered polling capability for asynchronous operations. Poll the identifier returned by the original submission; do not start a second generation to check progress.

## Selection rules

1. Prefer the capability that directly produces the requested UE deliverable.
2. Reuse an existing acceptable asset instead of generating a replacement.
3. Separate paid generation from free/local processing.
4. Separate content creation from placement, sequence creation, saving, and rendering.
5. Chain stages only when the earlier output is verified.
6. Ask for clarification only when the missing choice materially changes cost, target assets, mutation scope, or creative direction.
