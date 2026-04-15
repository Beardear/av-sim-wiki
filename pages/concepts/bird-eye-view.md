---
title: Bird's Eye View (BEV)
type: concept
created: 2026-04-07
updated: 2026-04-07
tags: [bev, sensor-fusion, perception, representation]
sources: [gemini-radar-doppler-dynamic-gs]
---

# Bird's Eye View (BEV)

## What It Is
A top-down 2D projection of 3D sensor data — as if viewed from a drone directly above the vehicle. The industry-standard spatial representation for AV perception and planning.

## Why BEV Dominates AV Perception

### Cars Drive in 2D
Vehicles operate on a flat road plane. For path planning, the relevant information is x,y position of obstacles — height is secondary. BEV captures exactly these spatial relationships.

### Computational Efficiency
Raw 3D point clouds have millions of unstructured coordinates. BEV converts the 3D problem into a 2D grid:
- Height information encoded as features (e.g., max height, height histogram per cell)
- 2D CNNs process the grid efficiently
- Orders of magnitude faster than processing raw 3D data

### Sensor Fusion in a Unified Space
The central value: cameras (perspective view), LiDAR (3D point cloud), and radar (range-azimuth) all use different native representations. BEV provides a **common coordinate frame** where all modalities can be projected and fused:

- Camera images → learned BEV projection (BEVFormer, BEVDet)
- LiDAR points → voxelization then pillar/BEV compression (PointPillars, CenterPoint)
- Radar returns → polar-to-Cartesian BEV projection

## Key BEV-Based Architectures
| System | Approach |
|--------|----------|
| CenterPoint | LiDAR pillars → BEV feature map → center-based detection |
| PointPillars | LiDAR points → vertical pillars → 2D BEV pseudo-image |
| BEVFormer | Camera images → spatial cross-attention → BEV features |
| BEVDet | Camera images → view transform → BEV detection |
| BEVFusion | Multi-modal BEV fusion (camera + LiDAR) |

## BEV for Simulation Validation
When visualizing simulated sensor data (e.g., from [[splatad]]):
- Plot ego vehicle at center
- Project simulated LiDAR hits onto x,y plane
- Compare BEV occupancy patterns between simulated and real data
- BEV visualization signals production-pipeline fluency to evaluators

## Related
- [[sensor-fusion]] — BEV as the fusion representation
- [[radar-simulation]] — radar data projected to BEV
- [[lidar-simulation]] — LiDAR-to-BEV pipeline
- [[mmdetection3d]] — implements BEV-based detection models
