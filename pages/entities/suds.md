---
title: SUDS
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [system, nerf, large-scale, dynamic-scenes]
sources: [zhou2026-av-scenario-review]
---

# SUDS (Scalable Urban Dynamic Scenes)

## What It Is
A modular NeRF approach for reconstructing dynamic large-scale urban scenes. Decomposes the scene into three independent hash table data structures: static, dynamic, and far-field radiant fields. Capable of decomposing a scene into static background, a single object, and its motion.

## Scale
- Reconstructed from **1700 videos** spanning hundreds of kilometers
- Geospatial footprint of tens of thousands of objects at 1.2 million frames
- One of the largest-scale neural scene reconstructions demonstrated

## Key Innovation
- Three-way decomposition: static + dynamic + far-field (handles unbounded sky/background)
- Unlabeled inputs used to learn semantic perception and scene flow, enabling downstream tasks
- Scalable to city-scale data

## Relevance
Demonstrates that neural reconstruction can scale far beyond single driving segments. If our project ever needs to scale [[splatad]] beyond individual scenes, SUDS' decomposition strategy (especially the far-field handling) is worth studying. A 3DGS equivalent at this scale would likely need [[citygaussian]]-style techniques.

## Related
- [[citygaussian]] — 3DGS approach to large-scale (but not as large as SUDS)
- [[emernerf]] — similar static/dynamic decomposition but at smaller scale
- [[3d-gaussian-splatting]] — no 3DGS system has matched SUDS' scale yet
- Source: [[zhou2026-av-scenario-review]] (Table 3)
