---
title: SplatAD
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [system, 3dgs, multi-sensor, cvpr-2025]
sources: [zhou2026-av-scenario-review]
---

# SplatAD

## What It Is
A 3D Gaussian Splatting system purpose-built for autonomous driving simulation. Published at CVPR 2025. Built on top of [[neurad]]-studio (nerfstudio fork for driving).

## Key Capabilities
- **Multi-sensor rendering**: Camera (RGB) + LiDAR (range, intensity, ray-drop) from a single Gaussian scene representation.
- **Dynamic actor decomposition**: Each tracked actor gets its own set of Gaussians tagged with an `id` field. Actors can be independently moved, removed, or re-posed.
- **Learned decoders**: CNN decoder for RGB refinement, MLP decoder for LiDAR intensity and ray-drop prediction. These close the gap between raw splatting output and realistic sensor data.
- **CUDA rasterization**: Custom CUDA kernels for both camera and LiDAR splatting. Orders of magnitude faster than pure Python.

## Architecture
1. **Scene representation**: Millions of 3D Gaussians, each with position, covariance, opacity, SH color coefficients, and optional per-point features.
2. **Camera rendering**: CUDA rasterize → raw feature maps → CNN decoder → RGB image.
3. **LiDAR rendering**: CUDA ray-Gaussian intersection → range + features → MLP decoder → intensity + ray-drop probability.
4. **Dynamic actors**: Gaussians tagged with actor `id`. Per-actor rigid transforms applied per-frame. Background Gaussians have `id=0`.

## Practical Experience (from our project)
- Trained on KITTI: 5M Gaussians, 30K steps. Camera rendering works well.
- **Lesson**: Hand-crafted synthetic Gaussians injected into a trained scene produced 0 changed pixels — actor manipulation MUST use SplatAD's own trained per-actor Gaussians, not external synthetic ones.
- **Lesson**: Pure Python splatting cannot match CUDA + learned decoder quality.
- 3 upstream bugs fixed: HDL-64E LiDAR elevation angles, PyTorch 2.6 `weights_only` compatibility, timestamp handling.

## Strengths
- Only system (as of early 2026) with joint camera + LiDAR Gaussian splatting at scale
- Dynamic actor support built-in (not a post-hoc addition)
- Active development, CVPR 2025 publication

## Weaknesses / Open Questions
- LiDAR intensity and ray-drop models are learned (not physics-based) — may not generalize across sensor types
- Scene editing (removal + inpainting) not built-in — requires external approaches like [[gs-roadpatching]], [[gaussian-editor]], or [[gaussian-grouping]]
- Per-scene training required (no cross-scene generalization)
- Actor decomposition uses tracked IDs — contrast with [[neural-scene-graph]] (learned scene graph) and [[emernerf]] (self-supervised)

> **Open question:** How does SplatAD's LiDAR fidelity compare quantitatively to LiDAR-GS and GS-LiDAR? Need head-to-head benchmarks.

## Related
- [[neurad]] — the base system SplatAD extends
- [[3d-gaussian-splatting]] — the underlying representation
- [[lidar-simulation]] — LiDAR-specific rendering techniques
- [[nuscenes]] — primary evaluation dataset
