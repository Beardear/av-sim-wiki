---
title: Street Gaussians
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [system, 3dgs, dynamic-scenes]
sources: [zhou2026-av-scenario-review]
---

# Street Gaussians

## What It Is
A 3DGS method for dynamic urban street scenes. Models the scene as separate Gaussian sets for static background and each dynamic object, similar to [[splatad]]'s approach.

## Key Technical Details (from [[zhou2026-av-scenario-review]])
- Uses a **4D spherical harmonic appearance model** for time-varying appearance
- Handles multiple moving objects by treating each as a separate dynamic Gaussian set
- Composite dynamic Gaussians used to handle articulated/deformable actors

## Differentiators from SplatAD
- Focuses primarily on camera rendering (no LiDAR)
- Earlier work that influenced SplatAD's design
- 4D SH model for appearance vs. SplatAD's CNN decoder

## Related
- [[splatad]] — extends this approach with multi-sensor support
- [[3d-gaussian-splatting]] — underlying representation
- [[scene-editing]] — enabled by per-object decomposition
- Source: [[zhou2026-av-scenario-review]] (Table 4)
