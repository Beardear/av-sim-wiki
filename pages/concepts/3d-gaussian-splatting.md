---
title: 3D Gaussian Splatting
type: concept
created: 2026-04-07
updated: 2026-04-12
tags: [3dgs, scene-representation, rendering, rasterization, spherical-harmonics]
sources: [zhou2026-av-scenario-review, gsplat-github-readme, gemini-nerf-gs-fundamentals, gemini-gs-speed-voxel-ray, gemini-depth-maps-datasets]
---

# 3D Gaussian Splatting (3DGS)

## What It Is
A scene representation that models a 3D scene as a collection of millions of anisotropic 3D Gaussians, each parameterized by:
- **Position** (mean): xyz center
- **Covariance** (shape): 3D ellipsoid defined by scale + rotation quaternion
- **Opacity**: alpha value
- **Color**: spherical harmonics coefficients (view-dependent appearance)

Rendering: project Gaussians onto the image plane, sort by depth, alpha-composite front-to-back. Fully differentiable, enabling end-to-end optimization from posed images.

## Why It Matters for AV Simulation
- **Real-time rendering**: 100+ FPS vs. [[nerf]]'s seconds-per-frame. Critical for closed-loop simulation.
- **Explicit representation**: Each Gaussian is a discrete element that can be tagged, grouped, moved, deleted. Enables scene editing (unlike NeRF's implicit field).
- **Multi-sensor extensibility**: Gaussians can store additional per-point attributes beyond color — intensity, semantics, material properties — enabling LiDAR and radar simulation from the same representation.

## Rendering Pipeline Detail
GS uses **tile-based rasterization** (object-centric), not ray marching (ray-centric):
1. Project all 3D Gaussians to 2D screen space ("splatting")
2. Divide screen into 16x16 pixel tiles
3. Sort Gaussians by depth (radix sort on GPU)
4. Alpha-blend front-to-back within each tile

This is fundamentally what GPUs are optimized for — sorting and blending — unlike NeRF's compute-heavy MLP inference per sample point.

**View-dependent color**: Instead of querying an MLP (as NeRF does), GS evaluates **Spherical Harmonics (SH)** coefficients stored per Gaussian — a fast polynomial evaluation, no neural network needed at inference.

## Adaptive Density Control (Training)
During optimization, GS dynamically adjusts the number and distribution of Gaussians:
- **Cloning**: High gradient + small Gaussian → duplicate it (densify detail)
- **Splitting**: Gaussian covers too much variance → split into two smaller ones
- **Pruning**: Opacity drops below threshold → delete it
The geometry physically evolves, growing Gaussians where detail is needed and removing them in empty space.

## The Floater Problem
GS training can produce semi-transparent Gaussians in empty space ("floaters") to reproduce view-dependent effects (reflections, specularities). These look correct to cameras but are physically hollow. When simulating LiDAR, the Monte Carlo ray-drop model correctly passes through floaters (low physical density), causing camera-LiDAR disagreement. This mismatch would cause **phantom braking** in a real AV. See [[sensor-fusion]] for how fusion handles this.

## AV-Specific Extensions
- [[splatad]]: Camera + LiDAR rendering, dynamic actor decomposition, learned decoders.
- [[street-gaussians]]: Dynamic urban scenes with per-object Gaussian sets and 4D SH appearance.
- [[driving-gaussian]]: Incremental static background + composite dynamic actors.
- [[lidar-gs|LiDAR-GS]]: Differentiable beam splatting on a range-view representation for LiDAR-specific rendering.
- GS-LiDAR: Panoramic LiDAR splatting with beam divergence modeling.
- [[citygaussian]]: Large-scale city-level 3DGS with LoD and divide-and-conquer.

## Key Challenges in AV Context
- **Unbounded scenes**: Driving scenes extend to the horizon. Standard 3DGS assumes bounded scenes. Solutions: spatial contraction, separate far-field models.
- **Dynamic objects**: Vanilla 3DGS is static. AV scenes have moving vehicles, pedestrians. Requires per-frame deformation or per-actor decomposition.
- **Sparse viewpoints**: AV data comes from a single trajectory (not 360-degree captures). Limited multi-view overlap → reconstruction artifacts in unseen regions.
- **Scale**: Urban scenes may need 5-20M Gaussians. Memory and training time scale accordingly.

## Comparison to NeRF
| Aspect | 3DGS | [[nerf]] |
|--------|------|------|
| Representation | Explicit (points) | Implicit (MLP) |
| Rendering | Tile-based rasterization (GPU-native) | Ray marching (MLP queries) |
| Rendering speed | Real-time (100+ FPS) | Slow (seconds/frame) |
| View-dependent color | Spherical Harmonics (fast polynomial) | MLP evaluation (expensive) |
| Empty space | Only processes existing Gaussians | Rays step through empty space |
| Editability | Direct manipulation | Difficult |
| Training speed | ~30 min | Hours |
| Memory | High (point storage) | Low (MLP weights) |
| Novel view quality | Comparable | Comparable |

## Canonical Implementation
- [[gsplat]] — the de-facto open-source CUDA rasterization library (Nerfstudio ecosystem, JMLR 2025). Per [[gsplat-github-readme]]: up to 4× less GPU memory and 15% less training time than the official 3DGS reference at equivalent PSNR/SSIM/LPIPS on mip-NeRF 360. Most 2025-era 3DGS systems — including [[splatad]] (via a fork) — build on gsplat's rasterization kernel.

## Related Concepts
- [[nerf]] — implicit neural alternative
- [[structure-from-motion]] — provides poses and initial point cloud (via [[colmap]])
- [[lidar-simulation]] — extending 3DGS to LiDAR modality
- [[radar-simulation]] — extending 3DGS to radar modality
- [[scene-editing]] — leveraging explicit Gaussians for actor manipulation
- [[sensor-fusion]] — floater problem requires cross-sensor validation
- [[sim-to-real-transfer]] — measuring reconstruction fidelity for downstream perception
