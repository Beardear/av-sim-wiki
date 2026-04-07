---
title: nuScenes
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [dataset, multi-sensor, benchmark]
sources: []
---

# nuScenes

## What It Is
A large-scale autonomous driving dataset collected in Boston and Singapore by Motional (formerly nuTonomy). The de facto standard benchmark for multi-sensor AV perception.

## Sensor Suite
- **6 cameras**: 360-degree surround view (front, front-left, front-right, back, back-left, back-right)
- **1 LiDAR**: Velodyne HDL-32E, 32 channels, ~34K points/sweep (after filtering)
- **5 radars**: Continental ARS408 long-range radars, front + 4 corners

## Scale
- 1000 scenes, each 20 seconds
- 1.4M camera images, 390K LiDAR sweeps
- 1.4M 3D bounding box annotations across 23 object classes
- Key frames at 2 Hz (with full annotations), sweeps at ~20 Hz

## Why It's Our Primary Dataset
- Multi-sensor (camera + LiDAR + radar) — matches our simulation target
- Well-supported by neurad-studio dataparser
- Large enough for meaningful evaluation
- Diverse conditions: day, night, rain, intersections, highways
- Strong baseline results from many perception models (CenterPoint, BEVFormer, etc.)

## nuScenes for Simulation
- Train [[splatad]] per-scene on nuScenes data
- Render novel views for all 6 cameras + LiDAR
- Compare rendered vs. real for domain gap measurement
- Use nuScenes detection benchmark to measure perception-level gap

## Sensor Rig Config
Need to create a YAML config for virtual-sensor-suite matching nuScenes geometry:
- 6 cameras with known intrinsics and extrinsics
- LiDAR (HDL-32E) with elevation angles
- 5 radars with positions and orientations

> **Open question:** Which nuScenes scenes are best for initial SplatAD training? Want diversity: day/night, rain/clear, intersection/highway.

## Related
- [[splatad]] — trains on nuScenes
- [[kitti]] — simpler dataset used for proof-of-concept
- [[waymo-open-dataset]] — comparable large-scale multi-sensor AV dataset
- [[sim-to-real-transfer]] — nuScenes as evaluation benchmark
