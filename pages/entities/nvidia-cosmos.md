---
title: NVIDIA Cosmos
type: entity
created: 2026-04-10
updated: 2026-04-10
tags: [system, nvidia, world-foundation-model, generative, synthetic-data]
sources: [nvidia-cosmos-web-research-2026-04-10]
---

# NVIDIA Cosmos

## What It Is
A platform of generative **World Foundation Models (WFMs)** for physical AI, launched by NVIDIA at CES January 2025 with a major update at GTC March 2025. Cosmos generates physics-aware synthetic video from text, images, video, or structured sensor data to train autonomous vehicles and robots. Open-weight models available via Hugging Face and NVIDIA NGC.

## Model Families

| Model | Input | Output | Architecture |
|-------|-------|--------|-------------|
| **Cosmos Predict** | text, image, video | virtual world states, trajectories | autoregressive + diffusion |
| **Cosmos Transfer** | segmentation, depth, LiDAR, pose | controllable photoreal video | diffusion |
| **Cosmos Reason** | video | natural language predictions | autoregressive (chain-of-thought) |

**Cosmos Tokenizer**: 8x more compression and 12x faster than competing video tokenizers.

## AV Relevance
- **Cosmos Transfer** is the most AV-relevant model: accepts LiDAR scans, depth maps, and segmentation as input, outputs photoreal driving video. This enables massive synthetic data amplification for perception training.
- **Omniverse Blueprint for AVs** pairs Cosmos Transfer with [[nvidia-drive-sim]] to vary weather, lighting, and sensor conditions on real driving scenarios.
- Adopters in AV space: Uber, Waabi, Wayve, Foretellix, Parallel Domain, Nexar, Oxa.

## Relationship to Neural Reconstruction
Cosmos takes a **generative** approach to sensor data synthesis, contrasting with the **reconstructive** approach of [[splatad]], [[neurad]], and [[unisim]]. The two are complementary:
- Reconstruction (3DGS/NeRF): high fidelity to a specific real scene, ground-truth geometry.
- Generation (Cosmos): diversity and scale, novel scenarios, domain randomization.

> **Open question:** Could Cosmos Transfer serve as a learned inpainting module for gaps in [[3d-gaussian-splatting]] reconstructions (sky, far-field, occluded regions)?

## Related
- [[nvidia-omniverse]] — the simulation platform Cosmos integrates with
- [[nvidia-drive-sim]] — AV simulation that uses Cosmos for data amplification
- [[sim-to-real-transfer]] — Cosmos aims to close the sim-to-real gap via data diversity
- [[lidar-simulation]] — Cosmos Transfer accepts LiDAR as structured input
- Source: [[nvidia-cosmos-web-research-2026-04-10]]
