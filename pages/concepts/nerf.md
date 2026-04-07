---
title: Neural Radiance Fields (NeRF)
type: concept
created: 2026-04-07
updated: 2026-04-07
tags: [nerf, scene-representation, rendering, implicit]
sources: [gemini-nerf-gs-fundamentals, gemini-gs-speed-voxel-ray]
---

# Neural Radiance Fields (NeRF)

## What It Is
An implicit neural scene representation that encodes a 3D scene as a continuous function approximated by a Multi-Layer Perceptron (MLP):

- **Input**: 5D coordinate — spatial position (x, y, z) + viewing direction (theta, phi)
- **Output**: radiance (RGB color) + volume density (sigma)

The scene does not exist as an explicit mesh or point cloud — it is a continuous mathematical function. Querying any 3D coordinate yields the color and opacity at that point.

## Rendering Pipeline (Volume Rendering)
NeRF renders images via **ray marching**: for each pixel, cast a ray from the camera into the scene, sample points along it, query the MLP at each point, then integrate using the volume rendering equation:

C(r) = integral of T(t) * sigma(t) * c(t) dt

Where:
- **T(t)** = transmittance = exp(-integral of sigma) — probability the ray hasn't hit anything before distance t
- **sigma(t)** = volume density — differential probability of ray termination (analogous to the linear attenuation coefficient in X-ray/CT physics)
- **c(t)** = radiance (color) at point t

> This is mathematically equivalent to X-ray CT reconstruction (Beer-Lambert law) plus an emission term. NeRF is "a CT scan where every voxel also glows with a specific color." — [[gemini-nerf-gs-fundamentals]]

## Point Sampling Strategy
1. **Stratified sampling (coarse pass)**: Divide ray into bins, sample one random point per bin (jittering converts aliasing into noise)
2. **Hierarchical sampling (fine pass)**: Evaluate coarse network (e.g., 64 points), treat output weights as a PDF, resample (e.g., 128 points) biased toward high-density regions. Evaluate fine network on all points combined.

## LiDAR in NeRF
Standard NeRF (2020) uses only RGB images. Modern AV-oriented NeRFs heavily use LiDAR as a geometric constraint:

- **Depth supervision**: Force density to peak at measured LiDAR distance
- **Empty-space skipping**: Force sigma=0 in free space before the LiDAR hit
- Eliminates "floaters" in textureless regions where cameras fail

## AV-Relevant NeRF Systems
| System | Key Feature |
|--------|------------|
| [[neurad]] | Multi-sensor NeRF for AV, camera + LiDAR |
| [[unisim]] | Closed-loop sensor simulation |
| [[emernerf]] | Self-supervised decomposition of static/dynamic |
| [[neural-scene-graph]] | Object-level NeRF scene graphs for dynamic scenes |
| [[suds]] | Scalable urban reconstruction from in-the-wild video |

## Limitations
- **Slow rendering**: Millions of MLP queries per frame → seconds/frame without acceleration
- **Per-scene training**: Each scene requires separate training (hours)
- **Implicit = hard to edit**: Cannot directly manipulate individual scene elements
- **Spectral bias**: MLPs struggle with high-frequency detail without positional encoding

## Comparison to 3DGS
See [[3d-gaussian-splatting#comparison-to-nerf]] for detailed comparison table. The key shift: NeRF hides the scene in network weights (implicit); GS exposes it as explicit parameters (Gaussians). GS achieves 100+ FPS real-time rendering vs NeRF's seconds-per-frame.

## Related
- [[3d-gaussian-splatting]] — explicit alternative, now dominant
- [[structure-from-motion]] — provides camera poses required by NeRF
- [[lidar-simulation]] — NeRF-based LiDAR rendering
- [[sim-to-real-transfer]] — NeRF fidelity for downstream perception
- [[gemini-gs-speed-voxel-ray]] — source: why GS is faster, voxel vs ray paradigms
