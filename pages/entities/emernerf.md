---
title: EmerNeRF
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [system, nerf, dynamic-scenes, self-supervised]
sources: [zhou2026-av-scenario-review]
---

# EmerNeRF

## What It Is
A NeRF system for autonomous driving that stratifies scenes into static, dynamic, and flow domains using self-supervised decomposition. An induced flow field was parameterized from the dynamic field to aggregate multi-frame features, improving reconstruction accuracy of dynamic objects.

## Key Innovation
- Three-component decomposition: static, dynamic, flow — all learned via self-supervision (no actor annotations required)
- Dynamic scenarios emphasize movement and temporal changes of traffic participants
- Flow scenarios focus on behavior patterns of materials and patterns in the scene

## Relevance
Interesting contrast to [[splatad]]'s approach which requires explicit actor tracking/annotations. EmerNeRF's self-supervised decomposition could be more scalable but potentially less controllable for scene editing.

## Related
- [[neurad]] — different NeRF approach to AV scenes
- [[splatad]] — uses supervised actor decomposition instead
- [[scene-editing]] — self-supervised decomposition may limit editing controllability
- Source: [[zhou2026-av-scenario-review]]
