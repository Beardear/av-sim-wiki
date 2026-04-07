---
title: Sensor Fusion
type: concept
created: 2026-04-07
updated: 2026-04-07
tags: [sensor-fusion, perception, multi-modal]
sources: [gemini-nerf-gs-fundamentals, gemini-radar-doppler-dynamic-gs, gemini-depth-maps-datasets]
---

# Sensor Fusion

## What It Is
Combining data from multiple sensor modalities (camera, LiDAR, radar) to produce a unified, robust perception of the environment. No single sensor is sufficient:

| Sensor | Strengths | Weaknesses |
|--------|-----------|------------|
| Camera | Rich semantics, texture, color | No metric depth, fails in glare/darkness |
| LiDAR | Precise 3D geometry, metric depth | No color/texture, sparse, expensive |
| Radar | Weather-robust, measures velocity | Low resolution, high clutter |

## Fusion Strategies

### Early Fusion
Combine raw sensor data before feature extraction. Example: project LiDAR points onto camera images to create RGBD input.

### Late Fusion
Run independent detection pipelines per modality, then merge results (e.g., NMS across detector outputs).

### Mid/BEV Fusion (Current Standard)
Extract features from each modality independently, project all to [[bird-eye-view]] space, then fuse feature maps. This is the dominant approach:
- Each sensor's features are transformed to a common BEV grid
- Fusion via concatenation, attention, or learned gating
- Joint detection head operates on fused BEV features

## The Floater Problem: Why Fusion Matters
3DGS reconstruction can produce "floaters" — semi-transparent Gaussians in empty space that create phantom obstacles in camera depth maps. LiDAR (measuring physical density via ray-drop probability) correctly passes through floaters.

If an AV relied solely on camera depth:
- Camera says "obstacle at 12.5m" (floater)
- LiDAR says "clear until 40.0m" (true surface)
- Result without fusion: **phantom braking** — dangerous false emergency stop

Proper sensor fusion trusts LiDAR's geometric measurement over camera's optical appearance in such conflicts.

## Calibration Requirements
Fusion requires precise spatial and temporal alignment of all sensors. The calibration chain (see [[kitti]]) transforms data between sensor coordinate frames:
- IMU → LiDAR (rigid body transform)
- LiDAR → Camera (extrinsic matrix)
- Camera → Rectified Camera (rotation for stereo alignment)
- Rectified Camera → Image (projection/intrinsic matrix)

Miscalibration causes sensor disagreement that degrades fusion quality.

## Key Frameworks
- [[mmdetection3d]]: Implements CenterPoint, BEVDet, PointPillars, BEVFusion
- [[autoware]]: Full AD stack with ROS2-based sensor fusion pipeline

## Related
- [[bird-eye-view]] — standard fusion representation
- [[radar-simulation]] — radar as fusion input
- [[lidar-simulation]] — LiDAR as fusion input
- [[sim-to-real-transfer]] — simulated sensor data must be fuseable like real data
