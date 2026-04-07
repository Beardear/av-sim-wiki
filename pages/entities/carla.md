---
title: CARLA
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [simulator, closed-loop, unreal-engine]
sources: [gemini-depth-maps-datasets]
---

# CARLA

## What It Is
An open-source, graphics-driven autonomous driving simulator built on Unreal Engine. The standard tool for **closed-loop** AV testing — where the ego vehicle's actions affect the simulation state.

**Repository**: `carla-simulator/carla`

## Key Capabilities
- Full physics sandbox: tire friction, weather, traffic lights, dynamic pedestrians/vehicles
- Robust Python API for spawning virtual sensors and controlling scenarios
- Supports camera, LiDAR, radar, GNSS, and IMU sensor simulation
- Programmable traffic scenarios for edge-case testing (jaywalkers, red-light runners, falling debris)

## Role in AV Development
| Aspect | CARLA (Graphics-Driven) | SplatAD/NeRF (Data-Driven) |
|--------|------------------------|---------------------------|
| Scene source | Handcrafted Unreal Engine assets | Reconstructed from real driving data |
| Realism | Stylized, limited sim-to-real transfer | Photorealistic, small domain gap |
| Dynamics | Full physics + traffic simulation | Static or compositional actors |
| Closed-loop | Yes — actions affect the world | Limited |
| Use case | Planning/control testing | Perception/sensor validation |

## Complementary to Neural Reconstruction
CARLA and 3DGS/NeRF serve different layers:
- **CARLA**: Test planning and control algorithms in a closed-loop world with physics
- **[[splatad]] / [[nerf]]**: Generate photorealistic sensor data for perception model training/validation
- Can be combined: use CARLA for scenario orchestration, pipe synthetic sensor data into perception stacks

## Related
- [[autoware]] — full AD stack that can consume CARLA sensor data
- [[sim-to-real-transfer]] — CARLA's domain gap is larger than neural reconstruction
- [[av-sim-framework]] — CARLA fits the "closed-loop simulation" step
