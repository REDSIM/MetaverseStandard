# Graphics and Shaders

[Home](../README.md) · [Concept](../Start%20Here.md) · [Architecture](../Architecture%20Overview.md) · [Glossary](../Reference/Glossary.md)

> Portable graphics need a reliable visual baseline before they expose programmable GPU features.

## At a Glance

- Every visual Resource provides a standard material fallback.
- [glTF PBR materials](https://registry.khronos.org/glTF/specs/2.0/glTF-2.0.html#materials) are the leading baseline for portable raster presentation.
- A custom shader language alone does not define a portable rendering pipeline.
- Custom graphics use a versioned portable profile prepared for the local graphics API by the Client. Content does not need a mandatory build for each OS.
- [Slang](https://shader-slang.org/slang/user-guide/) is a promising authoring and cross-compilation candidate, not a mandatory first-version package format.
- Compute, ray tracing, [CUDA](https://docs.nvidia.com/cuda/cuda-programming-guide/), and vendor features belong to explicit optional profiles with fallbacks.

## Graphics Profiles

The design defines one portable baseline and optional profiles:

1. **baseline graphics**: portable meshes, textures, animation, and standard materials.
2. **portable custom graphics**: a constrained programmable profile.
3. **advanced extensions**: compute, ray tracing, vendor-specific features, and experimental stages.

The standard does not require graphics, collision, and gameplay data to be authored or stored as separate layers. A World may update them together at runtime. Compatibility rules describe observable behavior and fallbacks rather than imposing one content-authoring workflow.

## Required Baseline

The first Client Profile should define:

- supported glTF/GLB subset and extensions.
- coordinate system, units, handedness, and transforms.
- color spaces, alpha behavior, precision, and texture formats.
- standard physically based materials and lighting inputs.
- animation and skinning limits.
- resource binding and memory budgets.
- a safe fallback material for unsupported effects.

This baseline is intentionally less expressive than native engines. It gives every compliant Client something predictable to render.

## What a Portable Shader Profile Must Define

Choosing [HLSL](https://learn.microsoft.com/en-us/windows/win32/direct3dhlsl/dx-graphics-hlsl), [WGSL](https://www.w3.org/TR/WGSL/), Slang, or another syntax is only one decision. Interoperability also requires:

- render passes and allowed pipeline stages.
- material-to-shader interface.
- parameter types, layout, and resource bindings.
- vertex attributes and coordinate conventions.
- texture, sampler, buffer, and storage rules.
- precision and derivative behavior.
- composition between World, Avatar, Item, and Client effects.
- supported features and numeric limits.
- validation, timeout, and workload policy.
- mandatory visual fallback.

Without these contracts, the same source language can still produce different or incompatible results.

## Candidate Profiles

| Profile | Intended scope | Current direction |
| --- | --- | --- |
| **Baseline Materials** | Standard raster materials | Required for initial core |
| **Portable Raster** | Constrained custom vertex/fragment effects | Design after baseline pipeline |
| **Portable Compute** | Bounded general GPU workloads | Separate high-risk extension |
| **Ray Tracing** | Optional ray pipelines | Future Client Profile |
| **Vendor Extension** | CUDA or device-specific features | Explicitly non-portable with fallback |

[WebGPU](https://www.w3.org/TR/webgpu/) and WGSL provide a useful model for cross-platform feature negotiation and validation. Slang provides modules, generics, interfaces, reflection, and multiple targets, making it attractive as an authoring source or optional source profile. Target support and feature coverage differ: each candidate toolchain needs a pinned-version test matrix, not an assumption that all its backends are equivalent.

## Portable Delivery Contract

Authoring language, distributed shader representation, and device-specific compiled output are different choices. A creator may use Slang or another tool without requiring every Client to load that tool's project format.

The agreed architecture is:

1. Authoring tools produce shader content conforming to a versioned portable Graphics Profile, plus a standard material fallback.
2. The Manifest binds shader files, imports, entry points, parameter interfaces, and optional target variants by hash.
3. The Client selects a compatible path before shader compilation, following [the common compatibility rules: Feature Negotiation](./Features%20and%20Permissions.md).
4. Its implementation translates the portable representation into the local backend's accepted form and lets the platform prepare executable GPU code.
5. Native compiled results remain disposable local caches, separate from the immutable Release.

The exact portable representation remains open: a restricted source language or a specified intermediate format. An arbitrary compiler's internal module format is not automatically a stable interchange standard. Likewise, [SPIR-V](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html) is not a universal binary accepted by every graphics API.

Publishers may include target-specific shader variants to reduce preparation time. Those variants do not replace the portable path for a Resource claiming portable custom graphics support, and signatures do not exempt them from validation. Standard-material fallback provides basic presentation on Clients without the custom profile. It does not promise to reproduce every effect.

A future graphics API can be supported by adding a Client backend for an existing profile, if its semantics can be implemented. New GPU features still need explicit extensions. Neither a translator nor a universal package makes every effect work on all devices. Older APIs may support only a smaller profile or fallback path.

## Compilation and Isolation

Shader input is untrusted even when it was signed by a Publisher. The Client controls the compiler and backend adapter. A Release cannot install a native compiler, driver, or plugin.

1. Verify selected shader files and their declared imports. No compiler-driven arbitrary network or filesystem access is allowed.
2. Validate profile version, language rules, stages, entry points, and static resource bounds.
3. Compile or translate outside the Client Core in a restricted worker, with time, memory, output-size, and specialization-count budgets.
4. Check generated parameter layout against the profile's renderer interface. Reflection can map bindings but cannot silently redefine their meaning or grant access to another Resource's buffers.
5. Validate backend intermediate code where supported, then create pipelines within memory, dispatch, and descriptor limits. Driver compilation also needs containment where the platform permits it.
6. Cache using content and import hashes, profile and interface versions, compiler options and version, backend, device, driver, and validation-policy version. Discard incompatible or invalid cache entries and rebuild from verified content.
7. On failure, select a declared fallback or block the affected required path. Use a placeholder for failed participant content instead of taking down the World.

Clients may prepare pipelines ahead of time or in the background, keeping the fallback active until ready. Compilation does not need to block the realtime presentation loop. The local preparation path works without a remote compiler or per-platform content service.

GPU work can still trigger driver bugs or starvation because the GPU is a shared failure domain. Static validation cannot prove that arbitrary compute always terminates, so runtime scheduling and watchdog policy remain necessary.

## Cross-Platform Fallback

- Every custom material references a standard material fallback.
- Missing optional stages disable the effect or select a declared fallback without silently breaking required interactions.
- Resource authors may provide quality tiers selected by Features and Limits.
- A Client may reject a shader that exceeds local safety policy even if it is syntactically valid.
- Worlds cannot require CUDA or one vendor API while claiming baseline portability.

## Current Proposals

- Adopt a constrained glTF material baseline for the MVP.
- Define the render pipeline and stable parameter ABI before selecting a mandatory custom language.
- Evaluate a strict WGSL/WebGPU-like raster subset for runtime portability.
- Evaluate Slang as an authoring tool and optional source profile, with pinned versions and conformance tests.
- Keep compute and ray tracing outside the initial core.

First test a small raster corpus on two independent Clients and different graphics backends: matching parameter interfaces, bounded visual differences, fallback, compilation failure, and cache invalidation. This architecture does not promote custom shaders into the initial material-only MVP.

See the official [glTF overview](https://www.khronos.org/gltf/), [WebGPU specification](https://www.w3.org/TR/webgpu/), and [Slang target documentation](https://shader-slang.org/slang/user-guide/targets) for the current candidate foundations.

Pipeline semantics, language choice, shader composition, compiler versioning, and advanced profiles are tracked in [Open Decisions and Roadmap](../Reference/Open%20Decisions%20and%20Roadmap.md).

## Related Topics

- Parent: [Start Here](../Start%20Here.md)
- World semantics: [Worlds and Sessions](../Concepts/Worlds%20and%20Sessions.md)
- Feature negotiation: [Features and Permissions](./Features%20and%20Permissions.md)
- Variant model: [Resource Model](../Concepts/Resource%20Model.md)
- Runtime containment: [Client and Runtime](./Client%20and%20Runtime.md)
