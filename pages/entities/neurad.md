---
title: NeuRAD
type: entity
created: 2026-04-07
updated: 2026-04-12
tags: [system, nerf, multi-sensor]
sources: [zhou2026-av-scenario-review, gsplat-github-readme]
---

# NeuRAD

## What It Is
A neural radiance field system for autonomous driving, implemented as a fork of nerfstudio (called neurad-studio). Serves as the foundation that [[splatad]] builds upon.

## Key Features
- Multi-sensor support (camera + LiDAR)
- Driving-specific dataparsers (nuScenes, PandaSet, Argoverse2, KITTI)
- Dynamic object handling
- nerfstudio-compatible training and evaluation pipeline

## Role in Our Project
NeuRAD-studio provides the infrastructure layer:
- Data loading and preprocessing (dataparsers)
- Training loop and config system
- Evaluation metrics
- Viewer and visualization

SplatAD replaces NeuRAD's NeRF-based rendering with 3DGS while reusing the rest of the infrastructure. The 3DGS rasterization path is provided by a fork of [[gsplat]] pinned at `neurad-studio/pyproject.toml:68`; the original NeRF-based NeuRAD path is independent of gsplat.

## Recognition in Literature
Per [[zhou2026-av-scenario-review]]: NeuRAD is noted as the first system to demonstrate **closed-loop evaluation** using neural scene reconstruction for AV. Dynamic and static elements are separated by positional embedding.

## Related
- [[splatad]] — the 3DGS model built on neurad-studio
- [[gsplat]] — the CUDA rasterization library neurad-studio pulls in (via the SplatAD fork) for 3DGS training
- [[nuscenes]] — primary dataset with neurad-studio dataparser support
- [[kitti]] — dataset used for initial proof-of-concept training
- [[unisim]] — another NeRF system targeting closed-loop evaluation
