---
title: TCLC-GS
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [system, 3dgs, lidar, multi-sensor]
sources: [zhou2026-av-scenario-review]
---

# TCLC-GS

## What It Is
A hybrid approach that combines LiDAR and camera data for Gaussian splatting. Designed a hybrid explicit color 3D mesh using LiDAR and camera data, along with an implicit hierarchical octree feature 3D representation, to enhance the properties of the 3D Gaussian function for splatting.

## Why It Matters
Directly relevant as a comparison point for [[splatad]] — both systems combine LiDAR and camera data, but TCLC-GS takes a different architectural approach (explicit mesh + implicit octree vs. SplatAD's pure Gaussian + learned decoders).

## Key Approach
- Uses LiDAR point cloud to initialize/constrain 3D mesh geometry
- Hierarchical octree for implicit features
- Combined advantages of LiDAR (accurate geometry) and cameras (appearance)

> **Open question:** How does TCLC-GS compare to [[splatad]] on nuScenes benchmarks? Does the explicit mesh give better geometry at the cost of editability?

## Related
- [[splatad]] — competing multi-sensor Gaussian splatting approach
- [[lidar-simulation]] — LiDAR data used as input, not just output
- [[ho-gaussian]] — another LiDAR+camera Gaussian method
- Source: [[zhou2026-av-scenario-review]] (Table 4)
