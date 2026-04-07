---
title: Wiki Index
type: index
updated: 2026-04-07
---

# Autonomous Driving Simulation Wiki — Index

## Overview
- [Overview](overview.md) — High-level synthesis of the AV simulation domain

## Concepts
- [3D Gaussian Splatting](pages/concepts/3d-gaussian-splatting.md) — Explicit point-based scene representation enabling real-time rendering and editing
- [Neural Radiance Fields (NeRF)](pages/concepts/nerf.md) — Implicit neural scene representation via volume rendering
- [LiDAR Simulation](pages/concepts/lidar-simulation.md) — Techniques for realistic LiDAR point cloud generation from neural scenes
- [Radar Simulation](pages/concepts/radar-simulation.md) — Radar cross-section, Doppler, and radar data generation from neural scenes
- [Sensor Fusion](pages/concepts/sensor-fusion.md) — Combining camera, LiDAR, and radar for robust perception
- [Bird's Eye View (BEV)](pages/concepts/bird-eye-view.md) — Top-down 2D representation for perception and planning
- [Stereo Vision](pages/concepts/stereo-vision.md) — Depth estimation from calibrated camera pairs
- [Structure from Motion (SfM)](pages/concepts/structure-from-motion.md) — Camera pose estimation and sparse reconstruction from images
- [Sim-to-Real Transfer](pages/concepts/sim-to-real-transfer.md) — Measuring and closing the domain gap between simulated and real sensor data
- [Scene Editing](pages/concepts/scene-editing.md) — Actor removal, insertion, trajectory modification, dynamic object simulation
- [AV Sim Framework (4-Step)](pages/concepts/av-sim-framework.md) — Reconstruct → Edit → Sim Import → Deploy pipeline (from Zhou et al. 2026)

## Entities — Systems (3DGS-based)
- [SplatAD](pages/entities/splatad.md) — Camera + LiDAR Gaussian splatting with dynamic actors (CVPR 2025)
- [Street Gaussians](pages/entities/street-gaussians.md) — Dynamic urban scenes with 4D SH appearance model
- [Driving Gaussian](pages/entities/driving-gaussian.md) — Incremental static + composite dynamic Gaussians for AV
- [CityGaussian](pages/entities/citygaussian.md) — Large-scale city-level 3DGS with LoD and divide-and-conquer
- [TCLC-GS](pages/entities/tclc-gs.md) — Hybrid LiDAR mesh + implicit octree Gaussian splatting
- [HO-Gaussian](pages/entities/ho-gaussian.md) — LiDAR + camera point density fusion for 3DGS
- [GS-RoadPatching](pages/entities/gs-roadpatching.md) — Object removal + road inpainting (SIGGRAPH Asia 2025)
- [AstroSplat](pages/entities/astrosplat.md) — Cross-scene actor transfer via Gaussian extraction
- [Gaussian Editor](pages/entities/gaussian-editor.md) — Text-guided fine-grained 3DGS editing
- [Gaussian Grouping](pages/entities/gaussian-grouping.md) — Semantic Gaussian grouping for structured scene editing

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

## Sources
- [Zhou et al. 2026](pages/sources/zhou2026-av-scenario-review.md) — Survey of NeRF/3DGS for AV scenario generation (~124 refs, 3-category taxonomy, 4-step framework)
- [Gemini Q&A — NeRF & GS Fundamentals](pages/sources/gemini-nerf-gs-fundamentals.md) — Ray casting, sampling, density, SfM, KITTI sensors, SLAM vs NeRF/GS
- [Gemini Q&A — GS Speed, Voxel vs Ray](pages/sources/gemini-gs-speed-voxel-ray.md) — Why GS is faster, voxel-driven vs ray-driven paradigms
- [Gemini Q&A — comma.ai & AV Comparison](pages/sources/gemini-comma-ai-av-comparison.md) — openpilot, Tesla FSD, Waymo, Zoox comparison
- [Gemini Q&A — Radar/Doppler, Dynamic GS, BEV](pages/sources/gemini-radar-doppler-dynamic-gs.md) — Radar cross-section, Doppler in 3DGS, compositional decomposition, BEV
- [Gemini Q&A — Depth Maps, Datasets, Frameworks](pages/sources/gemini-depth-maps-datasets.md) — Floaters, phantom braking, dataset comparison, open-source AV frameworks

## Comparisons
*(None yet — will be created as analysis questions arise)*

---

**Stats:** 11 concepts, 25 entities, 6 sources, 0 comparisons | Last updated: 2026-04-07
