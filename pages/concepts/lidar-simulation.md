---
title: LiDAR Simulation
type: concept
created: 2026-04-07
updated: 2026-04-11
tags: [lidar, sensor-sim, realism]
sources: [zhou2026-av-scenario-review, caesar2019-nuscenes, claude-nuscenes-lidar-discussion-2026-04-11, gemini-nerf-gs-fundamentals, gemini-depth-maps-datasets]
---

# LiDAR Simulation

## Why It Matters
LiDAR is the primary 3D sensing modality for most AV stacks. Simulated LiDAR must match real sensor characteristics closely — point density, intensity distribution, ray-drop patterns, range noise — or downstream perception models (e.g., [[centerpoint|CenterPoint]], [[pointpillars|PointPillars]]) will fail on real data. This is a **rare and valuable niche skill** — most simulation work focuses on cameras.

## Key Fidelity Dimensions
1. **Geometry (range accuracy)**: Chamfer distance between simulated and real point clouds.
2. **Intensity**: Per-point return intensity. Depends on material reflectance, incidence angle, range. Real sensors show characteristic intensity histograms per material.
3. **Ray-drop**: Not all emitted rays return a point (absorption, out-of-range, specular reflection). Realistic ray-drop patterns are critical — a sim that returns 100% of rays is immediately distinguishable from real data.
4. **Beam divergence**: Real LiDAR beams have non-zero width. At range, a single beam may hit multiple surfaces. Most sims model infinitely thin rays.
5. **Point density**: Must match the specific sensor model (e.g., Velodyne HDL-64E: 64 channels, ~130K points/sweep; Ouster OS1-128: 128 channels, ~260K points/sweep; [[nuscenes]] LiDAR: 32 beams, up to 1.4M pts/s, ≤70 m range per [[caesar2019-nuscenes]]).
6. **Noise**: Range noise, multipath, crosstalk.
7. **Motion skew / rolling shutter**: A spinning LiDAR is not a snapshot — it takes 50 ms per revolution at 20 Hz, during which the ego vehicle moves. Points at different azimuths within one scan are physically captured from different ego poses. A simulator that ignores this emits un-skewed output immediately distinguishable from real data, and the loss against raw GT becomes garbage. [[splatad]] handles this via rolling-shutter-aware rasterization — a CVPR 2025 contribution. See [[lidar-motion-compensation]] for the two design choices (compensate the GT vs. render-side rolling shutter) and why Design B is preferred.

## Approaches

### Ray-based (classical)
Cast rays from the sensor origin through the scene geometry. Simple and fast but requires explicit mesh/point cloud scene representation. Misses learned phenomena like ray-drop.

### Neural (NeRF-based)
Volumetric rendering along LiDAR rays through a neural radiance field. [[neurad]] and UniSim use this approach. Slow but can learn realistic intensity and ray-drop from data.

### Gaussian Splatting
- **[[splatad]]**: Ray-Gaussian intersection in CUDA, followed by MLP decoder for intensity and ray-drop. Current state of the art for joint camera+LiDAR.
- **[[lidar-gs|LiDAR-GS]]**: Differentiable beam splatting on a range-view representation — models beam divergence explicitly by splatting Gaussians along the beam cross-section rather than along infinitely thin rays. Also decomposes dynamic instances and outputs depth + intensity + ray-drop from one Gaussian scene.
- **GS-LiDAR**: Panoramic Gaussian splatting with beam divergence. Renders LiDAR as a panoramic depth image, then converts to point cloud.
- **Physically Based Neural LiDAR Resimulation**: Combines physics-based reflectance models with neural residual learning.

## Evaluation Metrics
| Metric | What it measures |
|--------|-----------------|
| Chamfer Distance | Geometric accuracy (point cloud shape) |
| Intensity MAE | Intensity prediction error |
| Ray-drop accuracy | Binary classification: does the ray return? |
| Range RMSE | Per-point range error |
| Detection AP (sim vs real) | Perception-level: does a detector trained on sim work on real? |

## LiDAR as Physical Reality Check
In 3DGS scenes, "floaters" (semi-transparent Gaussians in empty space) can fool cameras but not LiDAR. The Monte Carlo ray-drop model evaluates physical density — low-density floaters are passed through, correctly identifying them as non-physical artifacts. This makes LiDAR the authoritative sensor for geometric truth in simulation.

**Depth map cross-validation**: Compare camera depth tensor (`render_package["depth"]`, float32 meters) with LiDAR range at the same pixel. Agreement confirms calibration; disagreement flags floaters or miscalibration. See [[sensor-fusion]].

## Key Open Problems
- Intensity models are learned, not physics-based — they don't generalize across sensor types or environments.
- Ray-drop depends on material properties that aren't explicitly modeled in most 3DGS approaches.
- Beam divergence is ignored by most methods except [[lidar-gs|LiDAR-GS]].
- No standard benchmark for LiDAR simulation fidelity (unlike images, which have FID/LPIPS).
- [[radar-simulation]] fidelity is even less mature — most work ignores radar entirely.

> **Open question:** Can we improve [[splatad]]'s ray-drop MLP by conditioning on incidence angle and material-proxy features?

## Related
- [[3d-gaussian-splatting]] — underlying representation
- [[splatad]] — primary system under study
- [[lidar-motion-compensation]] — rolling-shutter handling as one fidelity dimension
- [[sim-to-real-transfer]] — downstream validation
- [[nvidia-drive-sim]] — industrial LiDAR simulation via Omniverse RTX ray tracing
- [[nvidia-cosmos]] — Cosmos Transfer accepts LiDAR scans as structured input for photoreal video generation
- [[radar-simulation]] — complementary active sensor simulation
- [[sensor-fusion]] — LiDAR as geometric ground truth in fusion
- [[open3d]] — point cloud processing and evaluation metrics
