---
title: PointPillars
type: entity
created: 2026-04-10
updated: 2026-04-11
tags: [perception, detector, lidar, baseline]
sources: [caesar2019-nuscenes]
---

# PointPillars

## What It Is
A LiDAR-only 3D object detector. Lang, Vora, Caesar, et al. — CVPR 2019. Encodes a raw point cloud into vertical "pillars" (a 2D BEV grid of pillars with no z-axis voxelization), runs a learned per-pillar feature encoder, then applies a standard 2D CNN backbone and detection head. Was the dominant LiDAR baseline before [[centerpoint]] and is still widely used as a fast reference.

## Architecture (high level)
- **Pillar feature net**: each non-empty pillar's points are passed through a small PointNet to produce a fixed-size feature vector.
- **2D CNN backbone**: pillar features are scattered back to a 2D BEV pseudo-image and processed with a standard 2D CNN — this is the main reason PointPillars is fast (no expensive 3D convs).
- **Detection head**: SSD-style anchor-based 3D box prediction.

## Why It Matters for This Project
PointPillars is grouped with [[centerpoint]] in `pages/concepts/lidar-simulation.md:13` as a representative downstream LiDAR perception model — the kind of consumer that simulated LiDAR must be realistic enough to fool. It is **not** the project's primary AP-delta evaluator (CenterPoint holds that role per `pages/concepts/sim-to-real-transfer.md:45`), but it serves as a faster, simpler alternative for early sanity checks and for older nuScenes baselines.

## Sensor Modality
LiDAR-only. The architectural simplification (no z voxelization) gives it a fast 2D-CNN inference path.

## Related
- [[lidar-simulation]] — named as a downstream LiDAR perception consumer
- [[centerpoint]] — successor and the project's primary AP-delta evaluator
- [[splatad]] — system producing the LiDAR output PointPillars would consume
- [[nuscenes]] — typical benchmark

## nuScenes Baseline Results (from [[caesar2019-nuscenes]])

- Single sweep: NDS 31.8%, mAP 21.9%, mAVE 1.21 m/s (val set)
- 10 accumulated sweeps: NDS 44.8%, mAP 28.8%, mAVE 0.30 m/s — temporal accumulation is critical
- Test set: NDS 45.3%, mAP 30.5% — similar mAP to MonoDIS (camera) but higher NDS due to better translation and velocity
- Pre-training (ImageNet vs KITTI vs none) has marginal impact on final performance at nuScenes scale

## Provenance
The canonical reference is Lang, Vora, Caesar, Zhou, Yang, Beijbom, "PointPillars: Fast Encoders for Object Detection from Point Clouds," CVPR 2019. Baseline results on nuScenes from [[caesar2019-nuscenes]].
