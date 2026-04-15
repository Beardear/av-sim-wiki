---
title: BEVFormer
type: entity
created: 2026-04-10
updated: 2026-04-10
tags: [perception, detector, camera, bev, transformer, baseline]
sources: []
---

# BEVFormer

## What It Is
A camera-only 3D perception model for autonomous driving. Li, Wang, Zhang, et al. — ECCV 2022. Uses a transformer architecture to lift multi-camera image features into a unified bird's-eye-view (BEV) representation, then runs detection (and optionally tracking / segmentation) in BEV space. Standard baseline on the [[nuscenes]] camera-only track.

## Architecture (high level)
- **BEV queries**: a learnable grid of query embeddings spanning the BEV plane.
- **Spatial cross-attention**: each BEV query attends to multi-view image features at the pixels it projects into, using known camera intrinsics and extrinsics.
- **Temporal self-attention**: BEV features at the current timestep attend to BEV features from prior timesteps, propagating information across frames for moving objects.
- No LiDAR input — purely camera-based.

## Why It Matters for This Project
BEVFormer is named alongside [[centerpoint]] as a baseline detector for measuring the sim-to-real gap (`pages/concepts/sim-to-real-transfer.md:23`). Because it consumes only camera input, it is the natural evaluator for the **camera side** of [[splatad]]-rendered [[nuscenes]] — complementary to CenterPoint's LiDAR side. Together they cover the project's two primary rendered modalities.

## Sensor Modality
Multi-view camera only. Requires known camera intrinsics/extrinsics — matches the [[nuscenes]] 6-camera surround rig.

## Related
- [[sim-to-real-transfer]] — listed as a baseline detector for AP-delta evaluation
- [[nuscenes]] — primary benchmark (camera track)
- [[splatad]] — system whose camera renderings BEVFormer would evaluate
- [[centerpoint]] — LiDAR-based counterpart in the project's evaluation plan

## Provenance
Written from general knowledge. `sources: []` — no paper ingested. The canonical reference is Li, Wang, Zhang, et al., "BEVFormer: Learning Bird's-Eye-View Representation from Multi-Camera Images via Spatiotemporal Transformers," ECCV 2022.
