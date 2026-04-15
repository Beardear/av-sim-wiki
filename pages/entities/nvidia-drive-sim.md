---
title: NVIDIA DRIVE Sim
type: entity
created: 2026-04-10
updated: 2026-04-10
tags: [system, nvidia, av-simulation, closed-loop, sensor-simulation]
sources: [nvidia-cosmos-web-research-2026-04-10]
---

# NVIDIA DRIVE Sim

## What It Is
NVIDIA's **AV-specific simulation platform**, powered by [[nvidia-omniverse]]. Designed for autonomous vehicle verification and validation at scale, supporting real-time multi-sensor simulation across L2-L5 autonomy levels. The most AV-relevant platform in NVIDIA's simulation ecosystem.

## Key Capabilities
- **Multi-sensor real-time simulation**: camera, radar, and LiDAR rendered simultaneously with physically accurate RTX ray tracing.
- **L2-L5 support**: sensor configurations scale from assisted driving to fully autonomous.
- **Sensor log replay**: replays real-world sensor logs as 3D scenes, then generates controlled variations for testing and synthetic data generation.
- **AlpaSim**: open-source AV simulation framework integrated into DRIVE Sim, featuring:
  - Realistic sensor modeling powered by Omniverse NuRec
  - Configurable traffic and policy models
  - Scalable closed-loop testing for rapid policy iteration across millions of virtual miles
- **NuRec (Neural Reconstruction)**: converts real-world sensor logs into 3D scene representations — conceptually analogous to [[splatad]] and [[neurad]] but integrated into NVIDIA's industrial pipeline.

## Comparison to Research Systems

| Aspect | DRIVE Sim | [[unisim]] | [[splatad]] |
|--------|-----------|-----------|-------------|
| Approach | Industrial sim platform | Research NeRF system | Research 3DGS system |
| Closed-loop | Yes (AlpaSim) | Yes (demonstrated) | Not yet demonstrated |
| Multi-sensor | Camera + radar + LiDAR | Camera | Camera + LiDAR |
| Neural recon | NuRec (proprietary) | NeRF | 3DGS |
| Scale | Production (millions of miles) | Research | Research |
| Open-source | AlpaSim is open-source | No | Yes |

## AV Relevance
DRIVE Sim is the **industrial-scale closed-loop simulation** platform that the research systems in this wiki are working toward. It combines traditional simulation (physics engine, traffic models, ray tracing) with neural techniques (NuRec, Cosmos integration) in a production-grade package.

The [[nvidia-cosmos]] Omniverse Blueprint for AVs pairs Cosmos Transfer with DRIVE Sim to amplify sensor data across weather and lighting conditions.

> **Open question:** What neural representation does NuRec use internally — NeRF, 3DGS, or mesh-based? Public documentation does not specify.

## Related
- [[nvidia-omniverse]] — the underlying simulation engine
- [[nvidia-cosmos]] — generative WFMs used for data amplification
- [[unisim]] — research-scale closed-loop NeRF simulation (closest research analogue)
- [[splatad]] — research-scale 3DGS reconstruction (complementary approach)
- [[lidar-simulation]] — DRIVE Sim's LiDAR simulation is a key capability
- [[sim-to-real-transfer]] — DRIVE Sim targets production-grade sim-to-real fidelity
- Source: [[nvidia-cosmos-web-research-2026-04-10]]
