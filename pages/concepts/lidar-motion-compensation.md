---
title: LiDAR Motion Compensation (Rolling Shutter)
type: concept
created: 2026-04-11
updated: 2026-04-11
tags: [lidar, sensor-sim, rolling-shutter, preprocessing, ego-pose]
sources: [claude-nuscenes-lidar-discussion-2026-04-11, caesar2019-nuscenes]
---

# LiDAR Motion Compensation

## The Problem

A spinning mechanical LiDAR (the kind used in most AV datasets, including [[nuscenes|nuScenes]]'s `LIDAR_TOP` and [[kitti|KITTI]]'s HDL-64E) is **not a snapshot sensor**. It takes one full revolution — 50 ms at 20 Hz, 100 ms at 10 Hz — to sweep its beams around 360°. During that rotation the ego vehicle is moving: at 50 km/h a car travels ~0.7 m per 50 ms revolution, and it may also pitch, roll, or yaw. Points captured at different azimuths within one scan were therefore measured from **different ego poses**.

Raw LiDAR returns typically come out of the sensor as tuples like `(x, y, z, intensity, ring_id, timestamp_offset)` in the sensor's *instantaneous* frame at firing time. If you dump all points from one scan directly into a single point cloud without correction, straight walls appear **sheared or curved** — points on one side of the car were captured earlier (from a pose the car has since left) and points on the other side were captured later (from a pose it hadn't reached yet). This artifact is called **motion skew** or, by analogy with cameras, **LiDAR rolling shutter**.

## The Compensation Procedure

Correcting motion skew requires, per point:

1. **Recover the exact capture time.** `t_point = t_scan_start + timestamp_offset`. The `timestamp_offset` tells you how far into the revolution this particular beam fired.
2. **Interpolate the ego pose at `t_point`.** Ego pose is sampled discretely (e.g. at ~100 Hz in nuScenes `ego_pose.json`). Interpolate between the two nearest samples — linearly for translation, via **SLERP** (spherical linear interpolation) for the rotation quaternion — to get `T_world_ego(t_point)`, a 4×4 rigid transform. See [[gps-imu]] for where ego pose comes from.
3. **Transform the point to world coordinates.** `p_world = T_world_ego(t_point) · T_ego_lidar · p_lidar`, where `T_ego_lidar` is the fixed sensor mounting calibration (stable per scene, stored in nuScenes's `calibrated_sensor` table).
4. **Transform back into a canonical frame.** Usually the ego pose at the scan's reference timestamp (keyframe moment): `p_canonical = T_world_ego(t_ref)⁻¹ · p_world`.

The net effect is: every point is shifted to "where it would have been if the car had stayed at `t_ref` the whole time." Straight walls become straight again. See [[sensor-coordinate-frames]] for the `T_ego_lidar` frame convention.

## Two Design Choices

When combining motion compensation with a neural scene reconstruction system like [[splatad]] or [[neurad]], there are two defensible designs — and they are not interchangeable.

### Design A — Compensate the ground truth

Un-warp every real LiDAR return into a single reference pose, then render from that single pose during training and compare. Simple: the renderer only needs one pose per scan and can be architecturally identical to a camera renderer. Downside: throws away the temporal information about *when* each beam was captured, and injects SLERP interpolation error directly into the training target.

### Design B — Rolling-shutter-aware rendering

Keep the real LiDAR in its raw, per-point-timed form. Teach the renderer to be time-aware: each ray cast uses the ego pose at the exact time that beam would have fired. Comparison happens directly against raw sensor data. The rendered scan therefore carries the *same* motion-skew signature as the real one, and when they are compared the skews cancel and the loss is clean.

**SplatAD uses Design B.** Rolling-shutter-aware rasterization for both camera and LiDAR is a headline contribution of the CVPR 2025 paper — see [[splatad]] for details. The upstream [[gsplat]] camera rasterizer was extended with a `lidar_rasterization` kernel that SplatAD owns; per-time pose handling is part of that kernel's contract.

> **Open question:** The exact mechanism by which SplatAD assigns time-varying poses inside the rasterizer — per-tile on a 2D panoramic representation, per-ray at kernel granularity, or otherwise — is not yet verified from the raw source. The SplatAD CVPR 2025 paper has not been ingested into the wiki. See [[claude-nuscenes-lidar-discussion-2026-04-11]] for the unverified recollection.

### Why Design B is preferred

Two reasons, both directly relevant to [[sim-to-real-transfer]]:

1. **Information preservation.** Motion skew is not noise — it encodes ego dynamics. A simulator that emits un-skewed LiDAR is immediately distinguishable from real data by any statistical or learned test, and is not plug-compatible with downstream detectors like [[centerpoint]] that were trained on skewed data.

2. **Loss fidelity.** Compensating the ground truth injects SLERP interpolation error directly into the training target. Design B confines that interpolation error to the rendering side; the raw sensor reading remains the immutable reference. Since `ego_pose` is only sampled at ~100 Hz while a 20 Hz LiDAR has points at effectively continuous `t_offset`, interpolation error is non-trivial.

The tradeoff is implementation complexity: a rolling-shutter-aware rasterizer cannot assume a single viewpoint per output frame. This is where the CUDA engineering in [[splatad]] and its [[gsplat]] fork lives.

## Preprocessing Cost in Practice

Regardless of which design is used, the per-point or per-tile ego-pose interpolation is a one-shot preprocessing step performed when a dataset is loaded. It is not free:

- On [[nuscenes]] trainval with a single 20-second training sequence, the [[splatad]] dataparser took **>10 minutes** to prepare the LiDAR data before the first training step. Contributing factors: loading the full trainval metadata index (all 850 scenes' JSON tables are hash-indexed even for single-scene training); computing per-point ego-pose interpolations for every LiDAR sweep in the sequence (not just the 2 Hz keyframes); and assembling camera intrinsic/extrinsic tables for all sensors.
- The cost is one-shot — results are cached in memory for the remainder of the run.
- At ~60k points per HDL-32E scan × 20 Hz × 20 seconds that is ~24M points per scene, each needing a SLERP-interpolated pose lookup. SLERP is not trivially vectorized, so this is a genuine few-minute CPU cost rather than an overhead artifact.

> **Open question:** Does SplatAD (or the upstream nuScenes dataparser) cache the compensated or interpolated-pose tables to disk across runs, or recompute every launch? Relevant for iterating on [[sim-to-real-transfer]] experiments where one might restart training many times.

## Relevance to Our Project

- **Phase 1 ([[sim-to-real-transfer]] domain gap):** rolling-shutter modeling is part of why SplatAD's LiDAR renders can plausibly be compared against real scans in the first place. If you're running LiDAR-level geometric metrics (Chamfer, intensity, ray-drop), you're implicitly trusting this mechanism.
- **Phase 2 (LiDAR realism niche):** any quantitative improvements to [[splatad]]'s LiDAR fidelity will eventually bottom out at questions about rolling-shutter handling, beam divergence, and incidence-angle-dependent reflectance. Motion compensation is the foundational layer of that stack.
- **Phase 3 (scene editing):** actor removal / insertion must preserve the rolling-shutter signature of the edited scan — a Design-B renderer inherits this automatically; a Design-A pipeline would have to re-skew the edited point cloud as a post-hoc step.

## Related

- [[splatad]] — rolling-shutter-aware rasterization as a CVPR 2025 contribution
- [[lidar-simulation]] — motion compensation as one fidelity dimension alongside ray-drop, intensity, and beam divergence
- [[gps-imu]] — the upstream origin of the ego poses that drive the interpolation
- [[nuscenes]] — provides the `ego_pose.json` sampling and the samples/sweeps distinction that determines how many LiDAR scans need compensation
- [[sensor-coordinate-frames]] — the `T_world_ego · T_ego_lidar` composition that underlies step 3 of the procedure
- [[sim-to-real-transfer]] — the downstream evaluation where the two designs give observably different results
- [[claude-nuscenes-lidar-discussion-2026-04-11]] — provenance for this page
