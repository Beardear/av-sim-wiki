---
title: gsplat
type: entity
created: 2026-04-12
updated: 2026-04-12
tags: [library, 3dgs, cuda, nerfstudio, infrastructure]
sources: [gsplat-github-readme]
---

# gsplat

## What It Is
An open-source CUDA library for rasterizing 3D Gaussians with Python/PyTorch bindings. Developed by the Nerfstudio team (UC Berkeley, NVIDIA, ShanghaiTech, Amazon, Meta, and others). Not a full reconstruction system — it is the **rasterization backbone** that other systems (including [[splatad]]) build on top of.

Inspired by the original SIGGRAPH 3D Gaussian Splatting paper, re-implemented for better speed and memory characteristics. See [[gsplat-github-readme]].

## Key Capabilities
- **Camera rasterization** (`gsplat.rendering.rasterization`): project Gaussians to image plane, alpha-composite, differentiable end-to-end.
- **Arbitrary batching** (May 2025): a single forward/backward can span multiple scenes and multiple viewpoints, unlike the original implementation which was single-scene.
- **NVIDIA 3DGUT integration** (April 2025): alternative rasterizer using the Unscented Transform (ray-traced Gaussians), contributed by NVIDIA Toronto.
- **PPISP bilateral-grid alternative** (January 2026): compensates appearance variation across training views.
- **Install targets**: PyPI with JIT CUDA build, source install, or pre-compiled wheels keyed to specific Python×PyTorch×CUDA combinations.

## Performance (per [[gsplat-github-readme]])
On the mip-NeRF 360 benchmark, vs. the official 3DGS implementation:
- Up to **4× less GPU memory**
- Up to **15% less training time**
- Equivalent PSNR, SSIM, LPIPS, and converged Gaussian count

Full benchmark details are in the white paper (arXiv:2409.06765), not yet ingested into this wiki.

## Relationship to Other Entities

- **[[3d-gaussian-splatting]]**: gsplat is the de-facto open-source implementation of the 3DGS rasterization primitive. When a paper says "we built on gsplat" (common in 2025), it means they started from this CUDA kernel.
- **[[splatad]]**: SplatAD's rasterizer is a **fork** of gsplat at `github.com/carlinds/splatad`, pinned in `neurad-studio/pyproject.toml:68`. The fork adds a `lidar_rasterization` kernel alongside upstream's camera `rasterization`, imported side-by-side at `neurad-studio/nerfstudio/models/splatad.py:61`. SplatAD = gsplat camera path + SplatAD-authored LiDAR path + learned CNN/MLP decoders.
- **[[neurad]]**: neurad-studio is the Nerfstudio fork NeuRAD publishes. It depends on the SplatAD gsplat fork for 3DGS training; the original NeRF-based NeuRAD path is independent.
- **Nerfstudio ecosystem**: same team as the nerfstudio viewer/config system used across all the above.

## Practical Notes (from our project)
- We train [[splatad]] via neurad-studio in `/workspace/SplatAD/`; every 3DGS forward/backward pass goes through the fork's `gsplat.rendering` module.
- Upstream gsplat is **not AV-aware**. No unbounded-scene contraction, no dynamic actor decomposition, no sensor coordinate-frame handling — those all live in SplatAD and neurad-studio, not in gsplat itself.
- Upstream gsplat does not rasterize LiDAR. That kernel exists only in the SplatAD fork.

## Strengths
- Mature, actively maintained, cleanly packaged (PyPI + wheels).
- Fast and memory-efficient relative to the original 3DGS reference implementation.
- Interoperable: same API is used by dozens of downstream 3DGS systems.
- Arbitrary batching enables multi-scene workflows that the original implementation could not support.

## Weaknesses / Open Questions
- The upstream library is scene-representation-agnostic; AV-specific capabilities must be added downstream.
- Fork divergence risk: if the SplatAD fork falls behind on upstream optimizations (including 3DGUT and PPISP), it inherits the lag.

> **Open question:** Would rebasing SplatAD onto current upstream gsplat yield meaningful gains — especially by adopting 3DGUT — or would the effort dominate the payoff?

> **Open question:** Does 3DGUT's ray-traced Unscented Transform rasterization handle thin-structure and far-field artifacts better than standard EWA splatting? Relevant to the Phase 1 failure-mode analysis.

## Related
- [[3d-gaussian-splatting]] — the technique this library implements.
- [[splatad]] — primary AV-domain consumer; uses a fork with added LiDAR rasterization.
- [[neurad]] — neurad-studio transitively depends on gsplat for 3DGS training.
- [[gsplat-github-readme]] — source for this page.
