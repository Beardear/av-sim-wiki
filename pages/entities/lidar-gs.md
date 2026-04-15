---
title: LiDAR-GS
type: entity
created: 2026-04-12
updated: 2026-04-12
tags: [system, 3dgs, lidar, re-simulation]
sources: []
---

# LiDAR-GS

## What It Is
A 3D Gaussian Splatting system purpose-built for **real-time LiDAR re-simulation**, published October 2024 (arXiv:2410.05111). Unlike general-purpose 3DGS methods that adapt camera rasterization to LiDAR as an afterthought, LiDAR-GS is designed from the ground up for active-sensor reconstruction: it simultaneously generates **depth, intensity, and ray-drop** for a single Gaussian scene representation.

LiDAR-GS is one of the two most directly comparable systems to [[splatad]]'s LiDAR rendering path (alongside GS-LiDAR, not yet on the wiki).

## Key Capabilities
- **Differentiable laser-beam splatting** on a **range-view representation** — rather than projecting Gaussians to an image plane and then converting to a point cloud, LiDAR-GS rasterizes directly in the sensor's native range-azimuth-elevation view.
- **Beam cross-section modeling** — projects laser beams onto micro cross-sections to eliminate artifacts that arise from treating beams as infinitely thin rays. Beam divergence is explicitly in the forward model, not a post-hoc correction.
- **Neural Gaussian Representation** — Gaussians carry view-dependent features (not just isotropic intensity) that are decoded downstream.
- **Dynamic-instance decomposition** — like [[splatad]], LiDAR-GS carves dynamic actors into separate Gaussian sets, enabling per-actor manipulation and avoiding background-foreground bleed.
- **Joint depth + intensity + ray-drop** — the three output channels share a single Gaussian scene, rather than being produced by separate passes.

## Relationship to Other Entities

- **[[splatad]]**: Direct competitor on the LiDAR rasterization axis. SplatAD splats Gaussians in a camera-style rasterizer with a custom `lidar_rasterization` kernel (see `neurad-studio/nerfstudio/models/splatad.py:61`), while LiDAR-GS operates natively in the range-view. Head-to-head benchmarks have not been compiled in this wiki yet (see the open question on `splatad.md`).
- **[[3d-gaussian-splatting]]**: Implements the 3DGS primitive, but for an active sensor rather than a passive camera. The rasterization math is different (beam cross-sections vs. pixel footprints) even though the scene representation is shared.
- **[[lidar-simulation]]**: LiDAR-GS is the one method on the concept page's list that explicitly models beam divergence — see `lidar-simulation.md:50`.
- **Phase 2** of `/workspace/PROGRESS.md`: listed as a primary reference for the "niche LiDAR expertise" phase. A head-to-head with SplatAD is the kind of comparison the phase is designed to produce.

## Strengths
- **Beam-physics-aware** — the range-view rasterization and cross-section beam model address one of the known sources of sim-to-real gap in SplatAD-style methods.
- **Multi-channel output from one scene** — depth, intensity, and ray-drop share state, which is architecturally cleaner than training separate heads.
- **Dynamic actor handling** — on par with [[splatad]] at this level of description.
- **Public code** per the arXiv abstract.

## Weaknesses / Open Questions
- The wiki has **not yet ingested the paper itself** — source page `sources:` is empty, all claims here are paraphrased from the arXiv abstract and carry the same reliability caveat as the stubbed [[centerpoint]] / [[bevformer]] / [[pointpillars]] pages.
- No **camera path**. LiDAR-GS is LiDAR-only; joint camera + LiDAR simulation from one scene — [[splatad]]'s headline strength — is not part of the design.
- Radar coverage is absent in the abstract.
- Which **datasets** the "large urban scene datasets" comparison was run on is unclear from the abstract. [[nuscenes]]? [[waymo-open-dataset]]? Both?

> **Open question:** Does LiDAR-GS model rolling shutter / ego-motion skew on par with [[splatad]]'s design-B rolling-shutter-aware rasterization (see [[lidar-motion-compensation]])? The 2026-04-11 session flagged this explicitly at `claude-nuscenes-lidar-discussion-2026-04-11.md:73`.

> **Open question:** What are the quantitative gaps in Chamfer distance, intensity MAE, and ray-drop accuracy between LiDAR-GS and [[splatad]] on a shared benchmark? This is exactly the Phase 2 head-to-head the project needs.

> **Open question:** Does the range-view rasterization constrain LiDAR-GS to a single, fixed LiDAR configuration (beam count, elevation pattern), or can it re-simulate across sensor types?

## Provenance
Stub page authored 2026-04-12 from the arXiv abstract at https://arxiv.org/abs/2410.05111. **No raw source is ingested yet.** Canonical citation for future `/wiki update`:

> Chen, Q., Yang, S., Du, S., Tang, T., Xie, R., Chen, P., Huo, Y. "LiDAR-GS: Real-time LiDAR Re-Simulation using Gaussian Splatting." arXiv:2410.05111, Oct 2024 (v1); Oct 2025 (v3).

Ingest the full PDF when Phase 2 head-to-head work needs kernel-level or benchmark-detail claims.

## Related
- [[splatad]] — primary comparator on the LiDAR rasterization axis
- [[3d-gaussian-splatting]] — the underlying scene representation
- [[lidar-simulation]] — fidelity dimensions including beam divergence that LiDAR-GS uniquely addresses
- [[lidar-motion-compensation]] — the rolling-shutter axis where SplatAD's contribution may or may not be matched by LiDAR-GS
