---
title: CenterPoint
type: entity
created: 2026-04-10
updated: 2026-04-10
tags: [perception, detector, lidar, baseline]
sources: []
---

# CenterPoint

## What It Is
A 3D object detector for LiDAR point clouds. Yin, Zhou, Krähenbühl — CVPR 2021. Predicts object centers as keypoints on a bird's-eye-view (BEV) heatmap rather than relying on predefined anchors, then regresses 3D box parameters and (optionally) refines with a second stage. One of the standard baselines on [[nuscenes]] and [[waymo-open-dataset]].

## Architecture (high level)
- **Backbone**: voxel-based (e.g. VoxelNet) or pillar-based (e.g. [[pointpillars]]) point-cloud encoder.
- **Center heatmap head**: predicts a per-class peak at each object's BEV center, plus regression heads for size, orientation, velocity, and (for nuScenes) attribute.
- **Two-stage variant**: a refinement stage gathers point features around each first-stage center and re-regresses the box.

## Why It Matters for This Project
CenterPoint is the **primary measurement instrument for the sim-to-real gap** in this project. The Phase 1 target is to train CenterPoint on [[splatad]]-rendered [[nuscenes]] data and compare its detection AP to a CenterPoint trained on real nuScenes — see the open question at `pages/concepts/sim-to-real-transfer.md:45`. A small AP delta means SplatAD's reconstruction is realistic enough that perception models cannot tell sim from real; a large delta is the wiki's domain-gap signal.

## Sensor Modality
LiDAR-only. Operates on point clouds; commonly paired with VoxelNet or [[pointpillars]] backbones.

## Related
- [[sim-to-real-transfer]] — CenterPoint named as the AP-delta evaluator (Phase 1 target)
- [[lidar-simulation]] — CenterPoint listed alongside [[pointpillars]] as a downstream consumer that simulated LiDAR must fool
- [[nuscenes]] — primary benchmark
- [[splatad]] — system whose rendered LiDAR CenterPoint will evaluate
- [[bevformer]] — camera-based counterpart used as another baseline

## Provenance
Written from general knowledge. `sources: []` — no paper ingested. The canonical reference is Yin, Zhou, Krähenbühl, "Center-based 3D Object Detection and Tracking," CVPR 2021. Drop the paper into `raw/` and run `/wiki update` for a proper source citation.
