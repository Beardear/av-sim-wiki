---
title: Argoverse 2
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [dataset, long-range, hd-maps]
sources: [gemini-depth-maps-datasets]
---

# Argoverse 2

## What It Is
A large-scale autonomous driving dataset maintained by the Argo AI legacy (now part of VW/Ford efforts). Distinguished by long-range LiDAR sensing and high-fidelity HD vector maps.

## Key Features
- **310-meter range LiDAR**: Significantly longer range than [[kitti]] or [[nuscenes]]
- **HD vector maps**: Detailed lane-level map annotations (lane boundaries, centerlines, crosswalks)
- **Multiple cameras**: Surround-view camera coverage
- **Diverse scenarios**: Urban driving in multiple US cities

## Strengths for AV Simulation
- Long-range sensing enables testing perception at extended distances
- Vector maps support "map-prior" approaches where the reconstruction uses lane geometry to improve rendering accuracy
- Strong community support and well-documented data format

## Comparison to Other Datasets
| Feature | Argoverse 2 | [[nuscenes]] | [[waymo-open-dataset]] |
|---------|------------|-----------|---------------------|
| LiDAR range | 310m | ~70m | ~75m |
| HD maps | Detailed vector maps | Basic map layers | Detailed |
| Radar | No | Yes (5 radars) | No public radar |
| Community size | Growing | Large | Large |

## Related
- [[nuscenes]] — comparable multi-sensor dataset
- [[waymo-open-dataset]] — comparable large-scale dataset
- [[kitti]] — earlier, simpler benchmark
- [[gemini-depth-maps-datasets]] — source: dataset comparison and framework survey
