---
title: 3D Gaussian Splatting
type: concept
created: 2026-04-07
updated: 2026-04-07
tags: [3dgs, scene-representation, rendering]
sources: [zhou2026-av-scenario-review]
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
- **Real-time rendering**: 100+ FPS vs. NeRF's seconds-per-frame. Critical for closed-loop simulation.
- **Explicit representation**: Each Gaussian is a discrete element that can be tagged, grouped, moved, deleted. Enables scene editing (unlike NeRF's implicit field).
- **Multi-sensor extensibility**: Gaussians can store additional per-point attributes beyond color — intensity, semantics, material properties — enabling LiDAR and radar simulation from the same representation.

## AV-Specific Extensions
- [[splatad]]: Camera + LiDAR rendering, dynamic actor decomposition, learned decoders.
- [[street-gaussians]]: Dynamic urban scenes with per-object Gaussian sets and 4D SH appearance.
- [[driving-gaussian]]: Incremental static background + composite dynamic actors.
- LiDAR-GS: Differentiable beam splatting for LiDAR-specific rendering.
- GS-LiDAR: Panoramic LiDAR splatting with beam divergence modeling.
- [[citygaussian]]: Large-scale city-level 3DGS with LoD and divide-and-conquer.

## Key Challenges in AV Context
- **Unbounded scenes**: Driving scenes extend to the horizon. Standard 3DGS assumes bounded scenes. Solutions: spatial contraction, separate far-field models.
- **Dynamic objects**: Vanilla 3DGS is static. AV scenes have moving vehicles, pedestrians. Requires per-frame deformation or per-actor decomposition.
- **Sparse viewpoints**: AV data comes from a single trajectory (not 360-degree captures). Limited multi-view overlap → reconstruction artifacts in unseen regions.
- **Scale**: Urban scenes may need 5-20M Gaussians. Memory and training time scale accordingly.

## Comparison to NeRF
| Aspect | 3DGS | NeRF |
|--------|------|------|
| Representation | Explicit (points) | Implicit (MLP) |
| Rendering speed | Real-time (100+ FPS) | Slow (seconds/frame) |
| Editability | Direct manipulation | Difficult |
| Training speed | ~30 min | Hours |
| Memory | High (point storage) | Low (MLP weights) |
| Novel view quality | Comparable | Comparable |

## Related Concepts
- [[lidar-simulation]] — extending 3DGS to LiDAR modality
- [[scene-editing]] — leveraging explicit Gaussians for actor manipulation
- [[sim-to-real-transfer]] — measuring reconstruction fidelity for downstream perception
