---
title: SplatAD
type: entity
created: 2026-04-07
updated: 2026-04-11
tags: [system, 3dgs, multi-sensor, cvpr-2025]
sources: [zhou2026-av-scenario-review, gsplat-github-readme, claude-nuscenes-lidar-discussion-2026-04-11]
---

# SplatAD

## What It Is
A 3D Gaussian Splatting system purpose-built for autonomous driving simulation. Published at CVPR 2025. Built on top of [[neurad]]-studio (nerfstudio fork for driving).

## Key Capabilities
- **Multi-sensor rendering**: Camera (RGB) + LiDAR (range, intensity, ray-drop) from a single Gaussian scene representation.
- **Dynamic actor decomposition**: Each tracked actor gets its own set of Gaussians tagged with an `id` field. Actors can be independently moved, removed, or re-posed.
- **Learned decoders**: CNN decoder for RGB refinement, MLP decoder for LiDAR intensity and ray-drop prediction. These close the gap between raw splatting output and realistic sensor data.
- **CUDA rasterization**: Custom CUDA kernels for both camera and LiDAR splatting. Orders of magnitude faster than pure Python. Implemented as a fork of [[gsplat]] (pinned at `neurad-studio/pyproject.toml:68` → `github.com/carlinds/splatad`) that extends upstream's camera `rasterization` with a new `lidar_rasterization` kernel; both are imported side-by-side at `neurad-studio/nerfstudio/models/splatad.py:61`.

## Architecture
1. **Scene representation**: Millions of 3D Gaussians, each with position, covariance, opacity, SH color coefficients, and optional per-point features.
2. **Camera rendering**: CUDA rasterize → raw feature maps → CNN decoder → RGB image.
3. **LiDAR rendering**: CUDA ray-Gaussian intersection → range + features → MLP decoder → intensity + ray-drop probability.
4. **Dynamic actors**: Gaussians tagged with actor `id`. Per-actor rigid transforms applied per-frame. Background Gaussians have `id=0`. On [[nuscenes]], `id` is bound to the dataset's `instance_token` so annotation cuboid tracks map directly onto actor-Gaussian sets.

## Rolling-Shutter Modeling

A headline contribution of the CVPR 2025 paper is that both the camera and LiDAR rasterizers are **rolling-shutter-aware**: different parts of a single output frame can use different ego poses. For a spinning LiDAR this is structural, not cosmetic — at 20 Hz the sensor takes 50 ms per revolution, over which the ego vehicle moves ~0.7 m at 50 km/h, so points at different azimuths within one scan are physically measured from different ego poses. See [[lidar-motion-compensation]] for the full rationale and the two possible designs.

SplatAD deliberately does **not** un-warp the ground-truth LiDAR into a single reference pose. Instead, the simulator produces output that carries the same motion-skew signature as the real sensor, and the loss is computed against raw, un-compensated ground truth. Benefits: (1) motion skew encodes ego dynamics and must be reproduced to stay plug-compatible with downstream detectors trained on skewed data; (2) interpolation error from the ~100 Hz `ego_pose.json` sampling is confined to the rendering side rather than injected into the training target.

> **Open question:** The exact mechanism — per-tile poses on a 2D panoramic LiDAR representation, per-ray poses at CUDA-kernel granularity, or another scheme — has not yet been verified from the raw source. The SplatAD CVPR 2025 paper is not yet ingested into the wiki. See [[claude-nuscenes-lidar-discussion-2026-04-11]] for the unverified recollection.

## Preprocessing Cost (empirical)

Launching `ns-train splatad ... nuscenes-data --sequence 0796 --version v1.0-trainval` on RTX 4090 hardware took **>10 minutes** of data-loading work before the first training step. Observed breakdown (from `/workspace/devlog.md`, 2026-04-11):

- Loading the full v1.0-trainval metadata index (all 13 JSONs for all 850 scenes are hash-indexed by the devkit on import, even when training on a single scene).
- Per-point or per-tile ego-pose interpolation (SLERP) across every LiDAR sweep in the training sequence — feeds the rolling-shutter-aware rasterizer described above.
- Camera intrinsic / extrinsic table assembly across all sensors.

One-shot cost — results are cached in memory for the rest of the run. Relevant if iterating through many scenes: the `train_nuscenes_scenes.sh` launcher eats this penalty once per scene, not once for the whole batch.

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

> **Open question:** How does SplatAD's LiDAR fidelity compare quantitatively to [[lidar-gs|LiDAR-GS]] and GS-LiDAR? Need head-to-head benchmarks. [[lidar-gs]] is the strongest direct comparator and a natural Phase 2 target.

## Related
- [[neurad]] — the base system SplatAD extends
- [[3d-gaussian-splatting]] — the underlying representation
- [[gsplat]] — the upstream CUDA rasterization library SplatAD's rasterizer is forked from
- [[lidar-simulation]] — LiDAR-specific rendering techniques
- [[lidar-motion-compensation]] — the rolling-shutter rationale and the Design-A vs Design-B tradeoff SplatAD resolves in favor of Design B
- [[nuscenes]] — primary evaluation dataset
- [[claude-nuscenes-lidar-discussion-2026-04-11]] — provenance for the rolling-shutter and preprocessing-cost sections
