---
title: Gaussian Editor
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [system, 3dgs, editing, text-guided]
sources: [zhou2026-av-scenario-review]
---

# Gaussian Editor

## What It Is
A text-guided 3DGS editing system that converts text commands to fine-grained geometry and texture editing in 3D space. Uses 3D Gaussian functions and text commands to freely edit 3D scenes.

## Key Capability
- Natural language instructions → scene modifications
- Fine-grained control over geometry and texture
- Works directly on Gaussian representations

## Relevance to AV Simulation
Text-guided editing could enable more intuitive scenario authoring — e.g., "add a pedestrian crossing the road" or "make the scene rainy" — rather than manual Gaussian manipulation. However, current text-guided methods lack the precision needed for safety-critical AV testing where exact actor positions matter.

## Related
- [[gaussian-grouping]] — complementary semantic grouping for structured editing
- [[scene-editing]] — broader concept page
- [[splatad]] — scene representation that could be edited with these techniques
- Source: [[zhou2026-av-scenario-review]] (Table 2)
