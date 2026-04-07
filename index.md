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
- [LiDAR Simulation](pages/concepts/lidar-simulation.md) — Techniques for realistic LiDAR point cloud generation from neural scenes
- [Sim-to-Real Transfer](pages/concepts/sim-to-real-transfer.md) — Measuring and closing the domain gap between simulated and real sensor data
- [Scene Editing](pages/concepts/scene-editing.md) — Actor removal, insertion, trajectory modification in reconstructed scenes
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
- [KITTI](pages/entities/kitti.md) — Classic AV benchmark, used for initial proof-of-concept
- [Waymo Open Dataset](pages/entities/waymo-open-dataset.md) — Large-scale multi-sensor AV dataset (5 LiDAR + 5 cam)

## Sources
- [Zhou et al. 2026](pages/sources/zhou2026-av-scenario-review.md) — Survey of NeRF/3DGS for AV scenario generation (~124 refs, 3-category taxonomy, 4-step framework)

## Comparisons
*(None yet — will be created as analysis questions arise)*

---

**Stats:** 5 concepts, 18 entities, 1 source, 0 comparisons | Last updated: 2026-04-07
