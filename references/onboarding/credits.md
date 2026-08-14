# Onboarding: credits & the free allocation

Credits are StudioTwin's currency for generation jobs. Use this to set user
expectations during onboarding. **These figures are the published dashboard costs
as of 2026-08-14 and change over time / vary per model — for a paid MCP call the
live tool definition is authoritative, not this page.**

Docs: https://docs.studiotwin.ai/docs/dashboard/guides/how-credits-work

## Free monthly allocation

- New accounts: **50 subscription credits / month** (reset each billing cycle).
- **Complete your profile → 100 / month, plus 50 added immediately.** Required
  fields: First name, Last name, Team size, Use case (Company is optional).
  Account Details → Edit Profile, or the Overview-page prompt.

## Credit types

| Type          | Behaviour                                                        |
| ------------- | --------------------------------------------------------------- |
| Subscription  | Monthly allocation, used first, resets each billing cycle.      |
| Top-Up        | Purchased separately, used after subscription, never expires.   |

Buy more: **[app.studiotwin.ai/dashboard/buy-credits](https://app.studiotwin.ai/dashboard/buy-credits)**
(or Buy Credits / Top Up in the dashboard). Top-Up credits are added immediately.

## Indicative costs per toolkit (verify live)

| Toolkit / tool                         | Model(s)                              | Credits (approx.) |
| -------------------------------------- | ------------------------------------- | ----------------- |
| Motion — Text to Motion                | HY Motion 1.0 (Tencent) / Kimodo (NVIDIA) | 5 per action  |
| Motion — Edit / Stitch / Trajectory    | Kimodo (NVIDIA)                       | 5 each            |
| Environment — Text to Env Map          | FLUX.1-schnell (Black Forest Labs)    | 20                |
| Environment — Image to Env Map         | FLUX.1-schnell (Black Forest Labs)    | 25                |
| Environment — Env Map to World         | HY World 1.0 (Tencent)                | 80                |
| Mesh — Image to 3D Mesh                | Hunyuan 3D V2.1 / Tripo3D P1 / V3     | 25 / 75 / 45      |
| Material — Texture to Material         | Patina (FAL)                          | 20                |
| Material — Text / Image to Material    | Patina (FAL)                          | resolution formula¹ |
| Audio — Text to Sound Effect           | ElevenLabs                            | duration formula² |

¹ Material (text/image) scales with resolution + upscale factor; Text-to-Material
`round(2 + 10·MP + 3·MP·upscale)`, Image-to-Material `round(13 + 10·MP + 3·MP·upscale)`
(upscale term ignored at 1x; 1 MP = 1024×1024).
² Sound effect `round(max(seconds, 5) · 0.6)`; auto-duration reserves 18 credits
(30 s max) upfront and refunds the unused remainder after generation.

When quoting a cost to a user before a paid call, prefer the number the **live
tool definition** returns; fall back to these figures only as a rough estimate and
say so.
