---
title: Gaussian Grouping
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [system, 3dgs, editing, segmentation]
sources: [zhou2026-av-scenario-review]
---

# Gaussian Grouping

## What It Is
A method that groups 3D Gaussians by semantic identity, enabling structured scene editing. A grouped set of 3D Gaussian distributions can be used to reconstruct the entire 3D scene and then selectively edit parts.

## Key Capability
- Semantic grouping of Gaussians (which Gaussians belong to which object/region)
- Reconstruct and split anything in a 3D scene
- Effective object deletion and integrated editing strategy

## Relevance to AV Simulation
Gaussian Grouping addresses a key challenge in [[scene-editing]]: knowing which Gaussians to manipulate. While [[splatad]] uses tracked actor IDs for this, Gaussian Grouping takes a more general approach that could work even without pre-existing tracking annotations — potentially useful for editing static scene elements (road markings, signs, vegetation) that aren't tracked as dynamic actors.

## Related
- [[gaussian-editor]] — complementary text-guided editing
- [[scene-editing]] — broader concept
- [[splatad]] — uses ID-based grouping instead of learned semantic grouping
- [[neural-scene-graph]] — earlier scene decomposition approach
- Source: [[zhou2026-av-scenario-review]] (Table 2)
