---
title: Sim-to-Real Transfer
type: concept
created: 2026-04-07
updated: 2026-04-11
tags: [domain-gap, evaluation, realism]
sources: [zhou2026-av-scenario-review, caesar2019-nuscenes]
---

# Sim-to-Real Transfer

## The Problem
The fundamental question in AV simulation: does a perception model trained (or validated) on simulated data perform equally well on real data? The gap between simulated and real performance is the **domain gap** or **sim-to-real gap**.

## Measuring the Gap

### Image-Level Metrics
- **PSNR / SSIM**: Per-frame pixel-level similarity. Easy to compute but don't correlate well with perceptual quality.
- **LPIPS**: Learned perceptual similarity. Better correlation with human judgment.
- **FID / KID**: Distribution-level metrics. Compare statistics of simulated vs. real image distributions. Better for measuring systematic biases.

### Perception-Level Metrics (the ones that matter)
- **Detection AP**: Train a detector ([[centerpoint|CenterPoint]], [[bevformer|BEVFormer]]) on sim data, evaluate on real data (and vice versa). The AP delta is the domain gap.
- **Tracking metrics**: MOTA/MOTP on sim-trained tracker vs. real data.
- **Segmentation mIoU**: Semantic segmentation model trained on sim, evaluated on real.

### LiDAR-Specific Metrics
- Chamfer distance between sim and real point clouds
- Intensity histogram divergence
- Ray-drop accuracy (precision/recall)
- Detection AP specifically for LiDAR-based detectors

## Approaches to Closing the Gap
1. **Better reconstruction**: Improve the 3DGS/NeRF model itself — more training data, better regularization, architectural improvements.
2. **Domain adaptation**: Train perception models with techniques that bridge the gap (adversarial training, style transfer).
3. **Hybrid real+sim**: Mix real and simulated data during perception training. Even imperfect sim data can improve robustness.
4. **Pseudo-simulation**: Use real data with augmentations guided by simulation (CORL approach).
5. **Physics-informed models**: For LiDAR, use physically-based reflectance and ray-drop models instead of purely learned ones.

## Key Reference Results
- SplatAD (CVPR 2025): Reports competitive PSNR/SSIM on nuScenes camera, but perception-level gap not extensively studied.
- UniSim: Shows that NeRF-based sim data can improve perception model robustness when mixed with real data.
- Pseudo-Simulation (CORL): Demonstrates that simulation-guided augmentation of real data outperforms pure sim data.

> **Open question:** What is the current detection AP gap when training [[centerpoint|CenterPoint]] on SplatAD-rendered nuScenes vs. real nuScenes? This is our Phase 1 target measurement.

## Related
- [[3d-gaussian-splatting]] — the reconstruction method
- [[splatad]] — the system we're measuring
- [[lidar-simulation]] — LiDAR-specific gap considerations
- [[nuscenes]] — primary evaluation dataset
- [[nvidia-cosmos]] — generative WFMs that aim to close the gap via data diversity at scale
- [[nvidia-drive-sim]] — industrial-scale AV sim targeting production-grade sim-to-real fidelity
