---
title: Driving Gaussian
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [system, 3dgs, dynamic-scenes]
sources: [zhou2026-av-scenario-review]
---

# Driving Gaussian

## What It Is
A 3DGS method for autonomous driving that uses an incremental static Gaussian function to sequentially and progressively model the static background of the entire scene. Dynamic actors are handled via composite dynamic Gaussians — each object is reconstructed individually, then composed into the scene.

## Key Approach
- Static background: incremental 3DGS trained progressively along the driving trajectory
- Dynamic actors: separate composite Gaussian per object, allowing independent manipulation
- LiDAR data processed before Gaussian splatting to reconstruct more detailed scenes and maintain relationships within the scene

## Differentiators
- Sequential/progressive static background modeling (vs. [[splatad]]'s single-pass training)
- Uses LiDAR as input for scene initialization (similar to [[tclc-gs]], [[ho-gaussian]])

## Related
- [[splatad]] — alternative multi-sensor 3DGS approach
- [[street-gaussians]] — similar per-object dynamic Gaussian decomposition
- [[3d-gaussian-splatting]] — base representation
- Source: [[zhou2026-av-scenario-review]] (Table 4)
