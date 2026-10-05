# Destination: three.js and WebGPU browser experiences

**Status: guidance.** This page covers what happens *after* [Web MCP](../connectors/web-mcp.md) has produced and downloaded an output that a three.js (WebGL or WebGPU) project will load. It adds no MCP surface. Web MCP still owns every cloud action, and the operating policy in [`SKILL.md`](../../SKILL.md) still applies. Option names below describe *intent*. Confirm the exact field in the live tool definition before relying on one.

## Boundaries

- Browser runtime code never calls StudioTwin MCP. It never embeds API keys, cloud URIs or presigned URLs. Presigned URLs expire and carry temporary credentials.
- Every output becomes a project file before the app uses it. Keep generation, download, optimisation and scene integration as separate stages.
- Keep the StudioTwin asset id and job id alongside each file you ship, so the asset can be re-resolved later.
- When an expired URL or a changed host requires the same asset again, re-resolve the existing asset. Do not regenerate it.

## Budget at generation time

Choose output weight when you submit, by the asset's role. Don't try to fix it in the browser afterwards.

| Role | Intent to request, where the live definition supports it |
| --- | --- |
| Hero, seen close up | High face limit, detailed textures. Budget one hero per view. |
| Mid-ground prop | Moderate face limit, standard textures. |
| Instanced scatter | Low face limit or low-poly topology, shared material. |
| Far LOD | Retopology or decimation of the *same* source mesh. |

- Generate LODs from the same mesh. Swapping to a differently generated far mesh reads as an object changing identity. Cross-fade between LODs with a screen-space dither instead of a hard switch.
- If geometry compression is requested, the GLB will require a matching decoder (see below).
- The highest texture-quality settings produce maps far larger than a phone can hold. Downsize them for delivery (see Textures).

## GLB runtime contract

A successful download or build does not prove the browser can decode the file.

1. Read `extensionsRequired` from each downloaded GLB.
2. Give every `GLTFLoader` that can receive the file one shared decoder per required capability:

| Extension | Runtime support |
| --- | --- |
| `EXT_meshopt_compression` | `loader.setMeshoptDecoder(MeshoptDecoder)` |
| `KHR_draco_mesh_compression` | One shared `DRACOLoader` with a versioned decoder path |
| `KHR_texture_basisu` | `loader.setKTX2Loader(ktx2)` with a transcoder path and `detectSupport(renderer)` |

3. Apply the same loader setup to model, animation and collider paths, not only to the first model that loads.
4. Stop with an actionable error on an unknown required extension.

## Spatial basis

- Record the project's units (metres), up axis and forward axis once.
- Where supported, use the live auto-scale and export-orientation options. Otherwise normalise scale, pivot (ground at y = 0, centred) and facing once, in a single asset wrapper. Do not scatter compensating rotations through gameplay, camera or animation code.
- Verify the bounds after load against the intended real-world size.

## Textures and materials

- Map the roles to three.js slots:

| Map | Slot | Colour space |
| --- | --- | --- |
| basecolor / albedo | `map` | sRGB |
| normal | `normalMap` | linear (no colour space) |
| roughness, metalness | `roughnessMap` / `metalnessMap`, or one packed texture (G = roughness, B = metalness) | linear |
| height | `displacementMap` only on a tessellated mesh; otherwise omit it or use parallax | linear |

- Tileable materials: set `RepeatWrapping`, choose the repeat from real-world scale, and set anisotropy to the renderer maximum on desktop.
- Delivery: prefer KTX2 (UASTC for normals, ETC1S or UASTC for colour). As a minimum, use JPG or WebP at 1–2K for web and 1K for mobile. Do not ship raw multi-megabyte PNG sets.
- Tooling: [glTF Transform](https://gltf-transform.dev) (`gltf-transform optimize`, `etc1s`, `uastc`, `meshopt`, `resize`) compresses and resizes GLBs and their textures in one step. KTX-Software's `toktx` converts standalone PBR maps to KTX2. Re-read `extensionsRequired` after optimising, because these tools add compression extensions that the loader must support.
- Fade tiled detail normals as their texels shrink below about 2 px. This keeps them from shimmering on phones.
- Verify which maps were actually produced before wiring them.

## Environment maps

- Lighting: load a ≤2K HDR through PMREM as `scene.environment`.
- Visible sky: display the dome from a separate downsample, for example an 8K JPG on desktop and 4K on mobile, mipmapped. Never ship large upscale outputs raw: a 16K equirect is hundreds of megabytes.
- When real 3D geometry forms the horizon, request an open horizon. A skyline or treeline baked into the environment conflicts with scene scale.
- Match intensity to the scene by comparing linear luminance, not by eye.

## Motion and rigs

- Rename clips after load and select them by your own names.
- To make a generated clip play in place, zero only the horizontal translation of the root bone and keep the vertical translation. Stripping more collapses the pose or removes jumps and bob.
- Drive walk playback rate from the clip's measured stride to prevent foot sliding.
- For many animated agents, bake bone matrices into a texture and draw them instanced. Do not run one mixer per agent.

## Audio

Deliver OGG or MP3. Create or resume the `AudioContext` inside the first user gesture so playback works on iOS. Honour the looping intent from the brief when setting loop points.

## Integration performance

- Pre-compile materials of newly imported assets behind the loading screen (`renderer.compileAsync(scene, camera)`). Otherwise the first view of each new material stalls the frame.
- Instance repeated props. When updating instance buffers, upload only the live range, and never issue a zero-length update range (WebGL2 `bufferSubData` with length 0 copies from the offset to the end of the source array, which is the whole buffer when the range starts at 0).
- Load the next section's assets before the camera reaches it.
- Size assets per graphics tier: fewer instances, smaller textures and a lower pixel-ratio cap on mobile.

## Companion skill for visual quality

This page covers only getting StudioTwin outputs into a three.js project. For the rendering around them (shadows, water, atmosphere, specular anti-aliasing, bloom, grading, and visual validation), install [Three.js Awesome Graphics Agent Skills](https://github.com/scottstts/Threejs-Awesome-Graphics-Agent-Skills) (licensed `MIT AND GPL-3.0-only`; check its notices before redistributing any of its material) alongside this skill, for example `npx threejs-awesome-graphics-agent-skills@latest install --agent claude-code`, and start from its `threejs-skill-router`.

## Verification

Report what actually ran:

1. **Files:** every expected output exists where the app loads it, and each GLB's required extensions are known.
2. **Build:** the project builds or typechecks.
3. **Browser, when authorised:** at least one real asset decodes, the canvas is not blank, the console has no decoder, MIME or CORS errors, and `renderer.info` draw calls, triangles and textures are within budget.
4. **Mobile:** do not claim mobile readiness without a mobile or phone-viewport run.

Then report asset ids, job ids, local paths and anything unverified, as [`SKILL.md`](../../SKILL.md) requires.
