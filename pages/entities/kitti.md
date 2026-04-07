---
title: KITTI
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [dataset, benchmark]
sources: []
---

# KITTI

## What It Is
Karlsruhe Institute of Technology and Toyota Technological Institute dataset. One of the earliest and most influential AV benchmarks. Simpler and smaller than [[nuscenes]] but useful for rapid prototyping.

## Sensor Suite
- 2 stereo camera pairs (grayscale + color)
- 1 LiDAR: Velodyne HDL-64E, 64 channels, ~130K points/sweep
- GPS/IMU

## Role in Our Project
Used for initial proof-of-concept:
- Trained [[splatad]] on KITTI: 5M Gaussians, 30K steps
- Verified camera rendering pipeline
- Identified key lessons (synthetic Gaussians don't work, need CUDA backend)

Now superseded by [[nuscenes]] for primary development.

## Related
- [[nuscenes]] — primary dataset going forward
- [[splatad]] — trained on KITTI for initial testing
