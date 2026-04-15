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
- [Neural Radiance Fields (NeRF)](pages/concepts/nerf.md) — Implicit neural scene representation via volume rendering
- [LiDAR Simulation](pages/concepts/lidar-simulation.md) — Techniques for realistic LiDAR point cloud generation from neural scenes
- [LiDAR Motion Compensation (Rolling Shutter)](pages/concepts/lidar-motion-compensation.md) — Per-point ego-pose interpolation and render-side rolling-shutter modeling for spinning LiDAR
- [Radar Simulation](pages/concepts/radar-simulation.md) — Radar cross-section, Doppler, and radar data generation from neural scenes
- [Sensor Fusion](pages/concepts/sensor-fusion.md) — Combining camera, LiDAR, and radar for robust perception
- [Bird's Eye View (BEV)](pages/concepts/bird-eye-view.md) — Top-down 2D representation for perception and planning
- [Stereo Vision](pages/concepts/stereo-vision.md) — Depth estimation from calibrated camera pairs
- [Structure from Motion (SfM)](pages/concepts/structure-from-motion.md) — Camera pose estimation and sparse reconstruction from images
- [Sim-to-Real Transfer](pages/concepts/sim-to-real-transfer.md) — Measuring and closing the domain gap between simulated and real sensor data
- [Scene Editing](pages/concepts/scene-editing.md) — Actor removal, insertion, trajectory modification, dynamic object simulation
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
- [KITTI](pages/entities/kitti.md) — Classic AV benchmark with stereo cameras, LiDAR, GPS/IMU
- [Waymo Open Dataset](pages/entities/waymo-open-dataset.md) — Large-scale multi-sensor AV dataset (5 LiDAR + 5 cam)
- [Argoverse 2](pages/entities/argoverse.md) — Long-range LiDAR (310m) + HD vector maps

## Entities — Frameworks & Tools
- [CARLA](pages/entities/carla.md) — Open-source Unreal Engine closed-loop AV simulator
- [Autoware](pages/entities/autoware.md) — Full-stack open-source AD framework on ROS2
- [MMDetection3D](pages/entities/mmdetection3d.md) — OpenMMLab 3D detection & sensor fusion toolbox
- [Open3D](pages/entities/open3d.md) — 3D geometry processing library for point clouds
- [COLMAP](pages/entities/colmap.md) — Standard open-source SfM pipeline

## Entities — Organizations
- [comma.ai / openpilot](pages/entities/comma-ai.md) — Open-source L2 ADAS, end-to-end neural driving

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
- [Gemini Q&A — NeRF & GS Fundamentals](pages/sources/gemini-nerf-gs-fundamentals.md) — Ray casting, sampling, density, SfM, KITTI sensors, SLAM vs NeRF/GS
- [Gemini Q&A — GS Speed, Voxel vs Ray](pages/sources/gemini-gs-speed-voxel-ray.md) — Why GS is faster, voxel-driven vs ray-driven paradigms
- [Gemini Q&A — comma.ai & AV Comparison](pages/sources/gemini-comma-ai-av-comparison.md) — openpilot, Tesla FSD, Waymo, Zoox comparison
- [Gemini Q&A — Radar/Doppler, Dynamic GS, BEV](pages/sources/gemini-radar-doppler-dynamic-gs.md) — Radar cross-section, Doppler in 3DGS, compositional decomposition, BEV
- [Gemini Q&A — Depth Maps, Datasets, Frameworks](pages/sources/gemini-depth-maps-datasets.md) — Floaters, phantom braking, dataset comparison, open-source AV frameworks

## Comparisons
*(None yet — will be created as analysis questions arise)*

---

**Stats:** 13 concepts, 26 entities, 7 sources, 0 comparisons | Last updated: 2026-04-15 _(post-merge — rerun `/wiki lint` to verify counts)_
