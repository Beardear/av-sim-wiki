---
title: Wiki Index
type: index
updated: 2026-04-12
---

# Autonomous Driving Simulation Wiki — Index

## Overview
- [Overview](overview.md) — High-level synthesis of the AV simulation domain

## Concepts
- [3D Gaussian Splatting](pages/concepts/3d-gaussian-splatting.md) — Explicit point-based scene representation enabling real-time rendering and editing
- [LiDAR Simulation](pages/concepts/lidar-simulation.md) — Techniques for realistic LiDAR point cloud generation from neural scenes
- [LiDAR Motion Compensation (Rolling Shutter)](pages/concepts/lidar-motion-compensation.md) — Per-point ego-pose interpolation and render-side rolling-shutter modeling for spinning LiDAR
- [Sim-to-Real Transfer](pages/concepts/sim-to-real-transfer.md) — Measuring and closing the domain gap between simulated and real sensor data
- [Scene Editing](pages/concepts/scene-editing.md) — Actor removal, insertion, trajectory modification in reconstructed scenes
- [AV Sim Framework (4-Step)](pages/concepts/av-sim-framework.md) — Reconstruct → Edit → Sim Import → Deploy pipeline (from Zhou et al. 2026)
- [GPS/IMU (Ego-Pose Sensors)](pages/concepts/gps-imu.md) — Fused localization sensors providing ground-truth ego-pose for neural reconstruction
- [Sensor Coordinate Frames](pages/concepts/sensor-coordinate-frames.md) — Camera (CV: Y-down) vs. LiDAR/radar (robotics: Z-up) coordinate conventions and transforms

## Entities — Systems (3DGS-based)
- [SplatAD](pages/entities/splatad.md) — Camera + LiDAR Gaussian splatting with dynamic actors (CVPR 2025)
- [Street Gaussians](pages/entities/street-gaussians.md) — Dynamic urban scenes with 4D SH appearance model
- [Driving Gaussian](pages/entities/driving-gaussian.md) — Incremental static + composite dynamic Gaussians for AV
- [CityGaussian](pages/entities/citygaussian.md) — Large-scale city-level 3DGS with LoD and divide-and-conquer
- [TCLC-GS](pages/entities/tclc-gs.md) — Hybrid LiDAR mesh + implicit octree Gaussian splatting
- [HO-Gaussian](pages/entities/ho-gaussian.md) — LiDAR + camera point density fusion for 3DGS
- [LiDAR-GS](pages/entities/lidar-gs.md) — Real-time LiDAR re-simulation via differentiable beam splatting on a range-view representation (Chen et al. 2024, arXiv:2410.05111)
- [GS-RoadPatching](pages/entities/gs-roadpatching.md) — Object removal + road inpainting (SIGGRAPH Asia 2025)
- [AstroSplat](pages/entities/astrosplat.md) — Cross-scene actor transfer via Gaussian extraction
- [Gaussian Editor](pages/entities/gaussian-editor.md) — Text-guided fine-grained 3DGS editing
- [Gaussian Grouping](pages/entities/gaussian-grouping.md) — Semantic Gaussian grouping for structured scene editing

## Entities — Libraries & Infrastructure
- [gsplat](pages/entities/gsplat.md) — Open-source CUDA rasterization library for 3DGS (Nerfstudio, JMLR 2025); SplatAD's rasterizer is a fork of this

## Entities — Systems (NeRF-based)
- [NeuRAD](pages/entities/neurad.md) — NeRF for AV; neurad-studio provides SplatAD's infrastructure
- [UniSim](pages/entities/unisim.md) — Closed-loop NeRF simulation for safety-critical AV testing
- [EmerNeRF](pages/entities/emernerf.md) — Self-supervised static/dynamic/flow decomposition
- [Neural Scene Graph](pages/entities/neural-scene-graph.md) — Foundational actor decomposition via scene graphs
- [SUDS](pages/entities/suds.md) — Scalable urban dynamic scenes (km-scale, 1700 videos, NeRF)

## Entities — Datasets
- [nuScenes](pages/entities/nuscenes.md) — Multi-sensor AV dataset (6 cam + LiDAR + 5 radar), 1000 scenes
- [KITTI](pages/entities/kitti.md) — Classic AV benchmark, used for initial proof-of-concept
- [Waymo Open Dataset](pages/entities/waymo-open-dataset.md) — Large-scale multi-sensor AV dataset (5 LiDAR + 5 cam)

## Entities — Platforms (NVIDIA)
- [NVIDIA Cosmos](pages/entities/nvidia-cosmos.md) — Generative World Foundation Models (Predict, Transfer, Reason) for physical AI synthetic data
- [NVIDIA DRIVE Sim](pages/entities/nvidia-drive-sim.md) — AV-specific simulation platform on Omniverse (L2-L5, AlpaSim, NuRec)
- [NVIDIA Omniverse](pages/entities/nvidia-omniverse.md) — 3D simulation engine with RTX ray tracing, USD, multi-sensor rendering

## Entities — Perception Models
- [CenterPoint](pages/entities/centerpoint.md) — LiDAR 3D detector; primary AP-delta evaluator for the sim-to-real gap
- [BEVFormer](pages/entities/bevformer.md) — Camera-only BEV transformer for 3D detection; baseline on nuScenes camera track
- [PointPillars](pages/entities/pointpillars.md) — Fast pillar-based LiDAR detector; older baseline still used for sanity checks

## Sources
- [Caesar et al. 2019 — nuScenes](pages/sources/caesar2019-nuscenes.md) — Full nuScenes dataset paper: sensor suite, car setup, coordinate frames, NDS/sAMOTA metrics, baselines
- [Claude GPS/IMU Discussion (2026-04-09)](pages/sources/claude-gps-imu-discussion-2026-04-09.md) — Internal Claude session, provenance for the GPS/IMU concept page (not an external citation)
- [Claude nuScenes & LiDAR Rolling-Shutter Discussion (2026-04-11)](pages/sources/claude-nuscenes-lidar-discussion-2026-04-11.md) — Internal Claude session, provenance for the LiDAR motion compensation concept page, the nuScenes data model section, and the SplatAD rolling-shutter subsection
- [Claude nuScenes Data-Model Verification Session (2026-04-12)](pages/sources/claude-nuscenes-data-model-discussion-2026-04-12.md) — Internal Claude session, provenance for the empirically verified trainval row counts, structural invariants, and phase-mapping table on the nuScenes entity page
- [gsplat GitHub README](pages/sources/gsplat-github-readme.md) — Nerfstudio's open-source CUDA 3DGS rasterization library, citation for Ye et al. 2025 JMLR
- [NVIDIA Cosmos & Related Platforms (2026-04-10)](pages/sources/nvidia-cosmos-web-research-2026-04-10.md) — Web research on Cosmos, Omniverse, DRIVE Sim ecosystem
- [Zhou et al. 2026](pages/sources/zhou2026-av-scenario-review.md) — Survey of NeRF/3DGS for AV scenario generation (~124 refs, 3-category taxonomy, 4-step framework)

## Comparisons
*(None yet — will be created as analysis questions arise)*

---

**Stats:** 8 concepts, 26 entities, 7 sources, 0 comparisons | Last updated: 2026-04-12
