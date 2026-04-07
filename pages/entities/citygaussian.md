---
title: CityGaussian
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [system, 3dgs, large-scale]
sources: [zhou2026-av-scenario-review]
---

# CityGaussian

## What It Is
A large-scale 3D Gaussian Splatting method that uses Level-of-Detail (LoD) selection and a divide-and-conquer approach to handle city-scale scenes. Achieves fast rendering at different scales by proposing tile-level detail selection and an aggregation strategy.

## Why It Matters
Most 3DGS methods (including [[splatad]]) train per-scene on relatively short driving segments. CityGaussian addresses scaling to much larger environments — potentially useful if our simulation needs to cover entire city blocks or longer routes.

## Key Approach
- Divide large scene into manageable chunks
- Train each chunk independently
- Hierarchical structure allows parallel processing and fast rendering
- LoD selection enables real-time rendering at varying distances

## Related
- [[3d-gaussian-splatting]] — base representation
- [[suds]] — NeRF equivalent at even larger scale (km-range, 1700 videos)
- [[scene-editing]] — editing at city scale requires efficient representation
- Source: [[zhou2026-av-scenario-review]] (Table 3)
