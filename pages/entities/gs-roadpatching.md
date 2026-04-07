---
title: GS-RoadPatching
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [system, scene-editing, inpainting, siggraph-2025]
sources: []
---

# GS-RoadPatching

## What It Is
A method for object removal and road surface inpainting in 3D Gaussian Splatting scenes. Published at SIGGRAPH Asia 2025.

## Relevance
After removing an actor from a [[splatad]] scene (by deleting its tagged Gaussians), the area behind the actor has no observed data — it appears as a hole. GS-RoadPatching fills these holes with plausible road surface, maintaining multi-view consistency.

## Key for Phase 3
This is the primary approach to study for the scene editing phase of our roadmap.

## Related
- [[scene-editing]] — the broader concept
- [[splatad]] — the scene representation being edited
- [[astrosplat]] — complementary (insertion vs. removal)
