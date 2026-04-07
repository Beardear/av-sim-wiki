---
title: HO-Gaussian
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [system, 3dgs, lidar, multi-sensor]
sources: [zhou2026-av-scenario-review]
---

# HO-Gaussian

## What It Is
A 3DGS method that combines LiDAR and camera point density to improve rendering quality in challenging areas. Focuses on boosting point density where cameras alone provide insufficient coverage.

## Relevance
Another multi-sensor 3DGS system to benchmark against [[splatad]]. The approach of using LiDAR to densify poorly-covered regions is complementary to SplatAD's learned decoder approach.

## Related
- [[splatad]] — competing approach
- [[tclc-gs]] — similar LiDAR+camera fusion philosophy
- [[lidar-simulation]] — leverages LiDAR for better reconstruction
- Source: [[zhou2026-av-scenario-review]] (Table 4)
