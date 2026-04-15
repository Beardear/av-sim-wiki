---
title: Autonomous Driving Simulation — Overview
type: overview
created: 2026-04-07
updated: 2026-04-10
tags: [overview, av-simulation]
sources: []
---

# Autonomous Driving Simulation

## The Problem
Autonomous vehicles need to handle billions of miles of diverse driving scenarios, but real-world testing is slow, expensive, and dangerous. Simulation bridges this gap by generating synthetic sensor data (camera, LiDAR, radar) that perception systems can train and validate against. The central challenge is **data realism** — simulated data must be close enough to real sensor data that models trained on it transfer to the real world without performance degradation.

## The Landscape (as of early 2026)

### Traditional Approaches
- **Game-engine sims** (CARLA, LGSVL): handcrafted 3D assets, physics-based rendering. High control but poor realism — large sim-to-real gap, especially for LiDAR and camera appearance.
- **Log replay**: replay real sensor logs with modified ego trajectories. Perfect realism for unchanged parts, but cannot modify other actors or generate novel viewpoints far from the original trajectory.

### Industrial Platforms (NVIDIA)
- **[[nvidia-omniverse]]**: 3D simulation engine with RTX ray-traced, physically accurate multi-sensor rendering. Foundation for NVIDIA's AV and robotics simulation.
- **[[nvidia-drive-sim]]**: AV-specific simulation on Omniverse. Multi-sensor (camera, radar, LiDAR), L2-L5. Includes AlpaSim (open-source closed-loop testing) and NuRec (neural reconstruction from sensor logs).
- **[[nvidia-cosmos]]**: Generative World Foundation Models (Predict, Transfer, Reason) that synthesize physics-aware video from text, images, or structured sensor data. Cosmos Transfer is directly AV-relevant — accepts LiDAR/depth/segmentation, outputs photoreal driving video. Open-weight. Complementary to the per-scene neural reconstruction approach below.

### Neural Reconstruction (Current Frontier)
The dominant emerging approach: reconstruct real-world scenes from sensor logs using neural representations, then re-render from novel viewpoints and with scene edits.

- **NeRF-based**: [[neurad]], [[unisim]], [[emernerf]]. Volumetric rendering, slow but high quality.
- **3D Gaussian Splatting (3DGS)**: [[3d-gaussian-splatting]]. Explicit point-based representation, real-time rendering, state-of-the-art quality. Leading systems: [[splatad]], [[street-gaussians]], [[driving-gaussian]], LiDAR-GS.
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
- **Scene editing consistency**: Removing an actor and inpainting the gap is hard — especially maintaining consistency across camera and LiDAR simultaneously.
- **Far-field and sky**: 3DGS struggles with unbounded regions. Solutions include separate sky models, contracted spaces, and diffusion-based infilling.
- **Generalization**: Current methods train per-scene. Few-shot or generalizable reconstruction across scenes remains open.

## This Wiki's Focus
This wiki tracks the state of the art in neural scene reconstruction for AV simulation, with particular depth on:
- [[3d-gaussian-splatting]] and its AV-specific variants
- [[splatad]] as the primary system under study
- [[lidar-simulation]] techniques and metrics
- [[sim-to-real-transfer]] methods and evaluation
- [[scene-editing]] for safety-critical scenario generation
- [[av-sim-framework]] — the proposed end-to-end pipeline from reconstruction to deployment
