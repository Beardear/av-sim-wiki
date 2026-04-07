---
title: AstroSplat
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [system, scene-editing, asset-transfer]
sources: []
---

# AstroSplat

## What It Is
A method for cross-scene actor transfer in 3D Gaussian Splatting. Extracts an actor's Gaussian set from one scene and inserts it into another with appearance harmonization.

## Key Claim
Claims 10^4x better insertion quality compared to [[splatad]]'s native actor insertion.

## Relevance
Primary approach to study for actor insertion in Phase 3 of the roadmap.

> **Open question:** What specifically makes AstroSplat's transfer so much better? Is it the appearance harmonization, the geometric alignment, or both?

## Related
- [[scene-editing]] — the broader concept
- [[splatad]] — the base scene representation
- [[gs-roadpatching]] — complementary (removal vs. insertion)
