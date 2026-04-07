---
title: COLMAP
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [sfm, tool, preprocessing]
sources: [gemini-nerf-gs-fundamentals]
---

# COLMAP

## What It Is
The industry-standard open-source [[structure-from-motion]] (SfM) and multi-view stereo (MVS) pipeline. Most NeRF and 3DGS codebases expect COLMAP output as input for camera poses and initial sparse point clouds.

## Role in AV Simulation Pipeline
1. Takes raw images as input
2. Extracts features, matches across views, triangulates 3D points
3. Runs bundle adjustment to optimize camera poses + geometry jointly
4. Outputs: camera intrinsics/extrinsics + sparse point cloud

This output feeds directly into [[3d-gaussian-splatting]] (as initialization point cloud + poses) and [[nerf]] (as poses).

## Limitations for AV Data
- AV data is sequential with limited viewpoint diversity (single trajectory) → SfM quality can be poor
- Textureless surfaces (road, sky) produce sparse/noisy reconstructions
- When LiDAR is available, direct LiDAR initialization (e.g., [[splatad]]) is preferred over COLMAP's SfM point cloud

## Related
- [[structure-from-motion]] — the algorithm COLMAP implements
- [[3d-gaussian-splatting]] — uses COLMAP output for initialization
- [[nerf]] — uses COLMAP poses
