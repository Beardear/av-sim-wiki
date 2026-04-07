---
title: KITTI
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [dataset, benchmark, stereo]
sources: [gemini-nerf-gs-fundamentals]
---

# KITTI

## What It Is
Karlsruhe Institute of Technology and Toyota Technological Institute dataset. One of the earliest and most influential AV benchmarks. Simpler and smaller than [[nuscenes]] but useful for rapid prototyping.

## Sensor Suite
- **2 stereo camera pairs**: grayscale + color (see [[stereo-vision]] for why both)
  - **Monochrome**: No Bayer filter → 100% light transmission, no demosaicing blur. Optimal for geometric tasks (stereo matching, visual odometry) requiring sub-pixel accuracy
  - **Color**: Bayer filter for RGB. Optimal for semantic tasks (object detection) requiring color context
- **1 LiDAR**: Velodyne HDL-64E, 64 channels, ~130K points/sweep, +-2 cm accuracy
- **GPS/IMU**: OXTS RT3003 — RTK-GPS (centimeter-level) + high-frequency IMU (100Hz+)

## Ground Truth Methodology
"Ground truth" means measured by instruments orders of magnitude more accurate than the algorithms being tested:
- **LiDAR** provides geometric ground truth (direct physical distance measurement vs camera's inference)
- **GPS/IMU** provides trajectory ground truth (RTK-GPS prevents global drift)
- Camera algorithms are the "student" graded against this "teacher"

## Calibration Chain
The sensor coordinate transforms (stored in `calib.txt`):
1. `Tr_imu_to_velo` — IMU to LiDAR frame (rigid body)
2. `Tr_velo_to_cam` — LiDAR to reference camera (extrinsic, critical for [[sensor-fusion]])
3. `R0_rect` — camera rectification (makes epipolar lines horizontal for stereo)
4. `P0-P3` — projection matrices (3D → 2D image)

## Benchmark Tasks
- **Stereo**: Dense depth from disparity
- **Optical flow**: Per-pixel motion vectors between frames
- **Visual odometry**: Camera trajectory estimation
- **3D object detection**: 3D bounding boxes in metric space
- **3D tracking**: Object association over time with IDs + velocity

## Role in Our Project
Used for initial proof-of-concept:
- Trained [[splatad]] on KITTI: 5M Gaussians, 30K steps
- Verified camera rendering pipeline
- Identified key lessons (synthetic Gaussians don't work, need CUDA backend)

Now superseded by [[nuscenes]] for primary development. KITTI is considered "legacy" — limited to front-facing view, no radar, less precise calibration by modern standards.

## Related
- [[nuscenes]] — primary dataset going forward
- [[waymo-open-dataset]] — higher fidelity alternative
- [[argoverse]] — long-range LiDAR alternative
- [[stereo-vision]] — KITTI's stereo-centric design
- [[splatad]] — trained on KITTI for initial testing
