---
title: gsplat — GitHub README (Nerfstudio)
type: source
created: 2026-04-12
updated: 2026-04-12
tags: [library, 3dgs, cuda, nerfstudio, infrastructure]
sources: [gsplat-github-readme]
---

# gsplat — GitHub README (Nerfstudio project)

## Citation

**Ye, V., Li, R., Kerr, J., Turkulainen, M., Yi, B., Pan, Z., Seiskari, O., Ye, J., Hu, J., Tancik, M., Kanazawa, A.** "gsplat: An open-source library for Gaussian splatting." *Journal of Machine Learning Research*, 26(34), 1–17, 2025.

- White paper (benchmarking, derivations): arXiv:2409.06765
- Repository: https://github.com/nerfstudio-project/gsplat
- Documentation: http://www.gsplat.studio/
- Local copy of README: `raw/gsplat-github-readme.md`

> **Note:** This source page is derived from the repository README only; the JMLR paper / arXiv white paper has not been ingested. Claims about API shape, kernel design, or benchmark specifics beyond the headline numbers below should be verified against the white paper before citing.

## Key Contributions

- **Open-source CUDA rasterization library** for 3D Gaussian splatting with Python bindings, inspired by the original SIGGRAPH 3DGS paper but re-implemented for better speed and memory efficiency (`raw/gsplat-github-readme.md:8`).
- **Measured efficiency gains** vs. the official 3DGS implementation on the mip-NeRF 360 benchmark: up to **4× less GPU memory** and up to **15% less training time**, at equivalent PSNR, SSIM, LPIPS, and converged Gaussian count (`raw/gsplat-github-readme.md:50`).
- **Arbitrary batching** (May 2025) — a single forward/backward pass can span multiple scenes and multiple viewpoints, enabling multi-scene training workflows (`raw/gsplat-github-readme.md:18`).
- **NVIDIA 3DGUT integration** (April 2025) — the Unscented Transform rasterizer from NVIDIA Toronto is now available through gsplat (`raw/gsplat-github-readme.md:22`).
- **PPISP** (Jan 2026) — a bilateral-grid alternative for compensating appearance variation across training views (`raw/gsplat-github-readme.md:16`).

## Technical Details Relevant to Our Domain

- **Install paths**: `pip install gsplat` (JIT CUDA build on first run), from source via git+https, or pre-built wheels keyed by Python×PyTorch×CUDA triple (`raw/gsplat-github-readme.md:28-45`).
- **Reference examples**: COLMAP-capture training, 2D image fitting with 3D Gaussians, large-scale real-time rendering, and NCore v4 capture training (`raw/gsplat-github-readme.md:66-69`). No AV-specific example in the upstream repo.
- **Institutional contributors**: UC Berkeley, NVIDIA, ShanghaiTech University, Amazon, Meta, IIIT, LumaAI, SpectacularAI, Aalto University, CMU (`raw/gsplat-github-readme.md:78-87`). This is the Nerfstudio ecosystem — the same lineage as [[neurad]]-studio.
- **Relation to [[splatad]]**: SplatAD's CUDA rasterizer is a **fork** of gsplat (`gsplat@git+https://github.com/carlinds/splatad.git` pinned at `neurad-studio/pyproject.toml:68`). SplatAD's model code imports both `rasterization` and a new `lidar_rasterization` from `gsplat.rendering` (`neurad-studio/nerfstudio/models/splatad.py:61`). So SplatAD = upstream gsplat camera rasterization + a custom LiDAR rasterization kernel added to the fork. This connection is documented from the repo itself, not from the README.

## Limitations & Open Questions

- The README is a pointer, not a spec. API shape, kernel-level optimizations, and benchmark methodology live in the [JMLR/arXiv white paper](https://arxiv.org/abs/2409.06765) — **not yet ingested**. If a claim needs to reference an equation, derivation, or benchmark detail, ingest that paper first.
- The 4× memory / 15% time numbers are stated without per-scene breakdown in the README; the white paper has the "Full report" cited at `raw/gsplat-github-readme.md:50`.
- The README does **not** claim AV-specific capabilities. LiDAR rasterization, unbounded driving scenes, and dynamic actor decomposition are **not** part of upstream gsplat — they exist only in the SplatAD fork.

> **Open question:** How divergent is the SplatAD fork from upstream gsplat? If upstream has added meaningful optimizations since the fork point, is it worth rebasing? (Not answerable from the README alone.)

> **Open question:** Does 3DGUT (NVIDIA Unscented Transform rasterizer) offer quality or efficiency improvements that would benefit SplatAD's camera rasterization path specifically? The integration is in upstream, not the fork.

## Informs These Pages

- [[gsplat]] — new entity page, primary consumer of this source.
- [[3d-gaussian-splatting]] — gsplat is the canonical CUDA library reference for this concept.
- [[splatad]] — SplatAD's rasterizer lineage is now documented with a direct repo citation.
- [[neurad]] — neurad-studio depends on the SplatAD gsplat fork for 3DGS training.
