---
title: Structure from Motion (SfM)
type: concept
created: 2026-04-07
updated: 2026-04-07
tags: [sfm, colmap, camera-poses, preprocessing]
sources: [gemini-nerf-gs-fundamentals]
---

# Structure from Motion (SfM)

## What It Is
A classic computer vision algorithm (photogrammetry) that simultaneously recovers two things from a set of 2D images:
1. **Sparse 3D geometry** — a rough point cloud of the scene
2. **Camera poses** — exact position + rotation of each image, plus camera intrinsics (focal length, distortion)

## Why It Matters for AV Simulation
NeRF and 3DGS are **not** SLAM systems — they do not solve for camera poses. They assume poses are known. SfM is the standard pre-processing step that provides these poses.

Pipeline: images → SfM (e.g., [[colmap]]) → camera poses → NeRF/GS training

## Algorithm Pipeline
1. **Feature extraction**: Detect keypoints (edges, corners) in each image
2. **Feature matching**: Find correspondences of the same 3D point across multiple images
3. **Triangulation**: Compute 3D coordinates by intersecting rays from matched features
4. **Bundle adjustment**: Joint optimization of all 3D point positions and camera parameters to minimize total reprojection error

## SfM vs SLAM
| Aspect | SfM | SLAM |
|--------|-----|------|
| Processing | Offline (batch) | Online (real-time, streaming) |
| Input | Unordered image set | Sequential sensor stream |
| Output | Sparse point cloud + poses | Sparse map + trajectory |
| Accuracy | High (global bundle adjustment) | Lower (local optimization, drift) |
| Speed | Slow (minutes-hours) | Fast (100Hz+) |

In modern AV pipelines, SLAM provides real-time localization while SfM/bundle adjustment provides the globally consistent poses needed for high-quality NeRF/GS reconstruction.

## Standard Tool: COLMAP
[[colmap]] is the industry-standard open-source SfM tool. Most NeRF/GS papers and codebases expect COLMAP output as input.

## Limitations
- Fails on textureless surfaces (white walls, uniform roads) — no features to match
- Scale ambiguity with monocular images (resolved by stereo baseline or LiDAR)
- Slow for large image sets (thousands of images)
- AV data is sequential with limited viewpoint diversity → can produce noisy reconstructions

> LiDAR initialization in GS (e.g., [[splatad]]) can bypass SfM for point cloud initialization, using the precise LiDAR points directly instead of the noisy SfM point cloud.

## Related
- [[nerf]] — requires SfM poses as input
- [[3d-gaussian-splatting]] — uses SfM point cloud as initialization
- [[colmap]] — standard SfM implementation
- [[stereo-vision]] — stereo provides scale for SfM
