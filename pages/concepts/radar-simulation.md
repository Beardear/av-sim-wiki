---
title: Radar Simulation
type: concept
created: 2026-04-07
updated: 2026-04-07
tags: [radar, sensor-sim, doppler, rcs]
sources: [gemini-radar-doppler-dynamic-gs]
---

# Radar Simulation

## Why It Matters
Radar is the most weather-robust AV sensor — it operates through rain, fog, snow, and darkness. Yet radar simulation is the least mature modality in neural scene reconstruction. Most 3DGS/NeRF work focuses on camera and LiDAR; radar fidelity is an open frontier.

## Key Physical Quantities

### Radar Cross Section (RCS)
A measure of how "visible" an object is to radar. Determines how much transmitted energy bounces back to the receiver. Measured in dBsm (decibels relative to 1 m^2).

Depends on:
- **Material**: Metal (highly reflective, large RCS) vs human body (absorbs/scatters, low RCS)
- **Geometry**: Flat surfaces facing radar → large RCS. Tilted surfaces → energy bounces away. 90-degree corners act as corner reflectors
- **Size**: Larger metal surfaces reflect more energy

| Object | Typical RCS |
|--------|------------|
| Semi-truck / bus | 10-100 m^2 |
| Standard car | 1-10 m^2 |
| Motorcycle / bicycle | 0.1-1 m^2 |
| Pedestrian | 0.1-1 m^2 |

**AV challenge**: A truck's echo can be 1000x stronger than a nearby pedestrian's, potentially "washing out" the pedestrian's faint return.

### Doppler Effect
Radar measures radial velocity via frequency shift of the returning wave. In AV context:
- **Ego-induced Doppler**: Even with a static scene, ego vehicle motion produces Doppler shifts against stationary objects (guardrails, parked cars). This is physically valid.
- **Actor Doppler**: Moving vehicles and pedestrians produce Doppler shifts from their own velocity.

## Radar in 3DGS Simulation

### The Static Scene Limitation
Standard 3DGS freezes the world into a static snapshot. Simulated radar Doppler will only reflect ego motion — dynamic actors are "baked in" as static geometry. Their true velocity is lost.

> "Our 3D simulation accidentally froze all the moving cars and pedestrians into statues. Our simulated radar can't measure their true movement — but it can accurately measure how fast our own car is driving past them." — [[gemini-radar-doppler-dynamic-gs]]

### Solution: Compositional Decomposition
Same approach as for camera/LiDAR (see [[scene-editing]]):
- Decompose scene into static background + object-centric actor Gaussians
- Apply per-frame SE(3) transforms to actor Gaussians
- Compute Doppler from both ego velocity and actor velocity vectors

## Radar Sensor Suites in Datasets
- [[nuscenes]]: 5x Continental ARS408 long-range radars (front + 4 corners)
- [[kitti]]: No radar
- [[waymo-open-dataset]]: No public radar data

> **Open question:** Can 3DGS be extended to model radar-specific phenomena (RCS variation with angle, multipath reflections, clutter) beyond simple Doppler?

## Related
- [[lidar-simulation]] — complementary active sensor simulation
- [[sensor-fusion]] — radar as input to fusion pipeline
- [[bird-eye-view]] — radar data projected to BEV for fusion
- [[nuscenes]] — primary dataset with radar data
