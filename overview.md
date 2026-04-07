---
title: Autonomous Driving Simulation — Overview
type: overview
created: 2026-04-07
updated: 2026-04-07
tags: [overview, av-simulation]
sources: [gemini-nerf-gs-fundamentals, gemini-radar-doppler-dynamic-gs, gemini-depth-maps-datasets]
---

# Autonomous Driving Simulation

## The Problem
Autonomous vehicles need to handle billions of miles of diverse driving scenarios, but real-world testing is slow, expensive, and dangerous. Simulation bridges this gap by generating synthetic sensor data (camera, LiDAR, radar) that perception systems can train and validate against. The central challenge is **data realism** — simulated data must be close enough to real sensor data that models trained on it transfer to the real world without performance degradation.

## The Landscape (as of early 2026)

### Traditional Approaches
- **Game-engine sims** ([[carla]], LGSVL): handcrafted 3D assets, physics-based rendering. High control but poor realism — large sim-to-real gap, especially for LiDAR and camera appearance. Still essential for **closed-loop testing** where ego actions affect the simulation.
- **Log replay** (open-loop): replay real sensor logs with modified ego trajectories. Perfect realism for unchanged parts, but cannot modify other actors, generate novel viewpoints, or test "what if the car braked?"

### Neural Reconstruction (Current Frontier)
The dominant emerging approach: reconstruct real-world scenes from sensor logs using neural representations, then re-render from novel viewpoints and with scene edits. Preprocessing via [[structure-from-motion]] (typically [[colmap]]) provides camera poses; LiDAR provides geometric initialization.

- **[[nerf]]-based**: [[neurad]], [[unisim]], [[emernerf]]. Implicit MLP representation, volumetric ray marching, slow but high quality. Rendering equation is mathematically equivalent to X-ray CT (Beer-Lambert + emission term).
- **[[3d-gaussian-splatting]] (3DGS)**: Explicit point-based representation, tile-based rasterization, real-time rendering (100+ FPS), state-of-the-art quality. Leading systems: [[splatad]], [[street-gaussians]], [[driving-gaussian]], LiDAR-GS.
- **Hybrid/diffusion**: Use generative models to fill gaps where reconstruction fails (sky, far-field, occluded regions).

### Key Capabilities Required
1. **Multi-sensor rendering**: Camera + LiDAR + radar from arbitrary viewpoints.
2. **Dynamic actors**: Decompose scene into static background + per-actor models. Move, remove, insert actors.
3. **Realism metrics**: Image-level (PSNR, SSIM, LPIPS, FID) and perception-level (detection AP on sim vs. real).
4. **LiDAR fidelity**: Point density, intensity, ray-drop patterns, range noise must match real sensors.
5. **Scene editing**: Remove actors (with inpainting), insert actors from other scenes, modify trajectories.
6. **Scalability**: Process large datasets ([[nuscenes]]: 1000 scenes, [[waymo-open-dataset]]: 1000+ segments). Systems like [[suds]] have demonstrated km-scale reconstruction from 1700 videos.

## Key Open Problems
- **Sim-to-real gap** for perception: models trained on reconstructed data still underperform vs. real data. Closing this gap is the central research challenge.
- **LiDAR realism**: Most work focuses on cameras. LiDAR simulation quality lags behind — ray-drop prediction, intensity modeling, and beam divergence are underexplored.
- **[[radar-simulation]]**: Even less mature than LiDAR. Radar cross-section (RCS) modeling, Doppler from dynamic actors, and multipath effects are barely addressed in neural reconstruction.
- **Floaters and phantom braking**: 3DGS can produce semi-transparent "floaters" in empty space. Cameras see them as obstacles; LiDAR correctly passes through. This sensor disagreement causes phantom braking. [[sensor-fusion]] must resolve such conflicts.
- **Dynamic objects in static scenes**: Standard 3DGS freezes the world. Industry uses compositional decomposition (static background + object-centric actor Gaussians with SE(3) transforms), but novel viewpoints of actors remain a challenge.
- **Scene editing consistency**: Removing an actor and inpainting the gap is hard — especially maintaining consistency across camera and LiDAR simultaneously.
- **Far-field and sky**: 3DGS struggles with unbounded regions. Solutions include separate sky models, contracted spaces, and diffusion-based infilling.
- **Generalization**: Current methods train per-scene. Few-shot or generalizable reconstruction across scenes remains open.

## The Broader Ecosystem

### Perception Pipeline
Simulated sensor data feeds into the perception stack. The standard spatial representation is [[bird-eye-view]] (BEV) — a top-down 2D grid where camera, LiDAR, and radar features are fused. Key frameworks: [[mmdetection3d]] (detection/fusion models), [[autoware]] (full AD stack on ROS2).

### Datasets
| Dataset | Sensors | Strength |
|---------|---------|----------|
| [[nuscenes]] | 6 cam + LiDAR + 5 radar | Multi-modal, 360-deg, rain/night |
| [[waymo-open-dataset]] | 5 cam + 5 LiDAR | Highest fidelity, 10Hz labels |
| [[kitti]] | Stereo cam + LiDAR + GPS/IMU | Classic benchmark, calibration chain |
| [[argoverse]] | Cameras + 310m LiDAR | Long-range, HD vector maps |

### Industry Players
- **L4 Robotaxis**: Waymo (sensor fusion, geofenced), Zoox (custom vehicle)
- **L2 ADAS**: Tesla FSD (vision-only, end-to-end), [[comma-ai]] (open-source, end-to-end)

## This Wiki's Focus
This wiki tracks the state of the art in neural scene reconstruction for AV simulation, with particular depth on:
- [[3d-gaussian-splatting]] and [[nerf]] — scene representation fundamentals
- [[splatad]] as the primary system under study
- [[lidar-simulation]] and [[radar-simulation]] — multi-sensor fidelity
- [[sensor-fusion]] and [[bird-eye-view]] — perception pipeline integration
- [[sim-to-real-transfer]] methods and evaluation
- [[scene-editing]] for safety-critical scenario generation
- [[av-sim-framework]] — the proposed end-to-end pipeline from reconstruction to deployment
- [[structure-from-motion]] and [[stereo-vision]] — preprocessing fundamentals
