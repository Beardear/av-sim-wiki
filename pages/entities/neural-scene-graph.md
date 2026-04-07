---
title: Neural Scene Graph
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [system, nerf, actor-decomposition, foundational]
sources: [zhou2026-av-scenario-review]
---

# Neural Scene Graph

## What It Is
A foundational NeRF-based method that introduced scene graph decomposition for dynamic driving scenes. Encodes object transformations and brightness, allowing the system to make the right decisions under safety control.

## Why It Matters
One of the earliest works to demonstrate actor-level decomposition in neural scene representations for AV. The scene graph paradigm — static background + per-actor nodes with learned transformations — became the template that later systems ([[splatad]], [[street-gaussians]], [[driving-gaussian]]) adopted for 3DGS.

## Key Capabilities
- Model improvement and editing via actor decomposition
- Actor position/rotation manipulation
- Pioneered the decomposed scene representation approach for AV

## Related
- [[splatad]] — adopted the per-actor decomposition paradigm for 3DGS
- [[scene-editing]] — Neural Scene Graph enabled the first actor-level edits in neural scenes
- [[emernerf]] — later self-supervised alternative to explicit scene graphs
- Source: [[zhou2026-av-scenario-review]] (Table 4)
