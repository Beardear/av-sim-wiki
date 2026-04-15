---
title: GPS/IMU (Ego-Pose Sensors)
type: concept
created: 2026-04-09
updated: 2026-04-11
tags: [sensor, localization, ego-pose, ground-truth]
sources: [claude-gps-imu-discussion-2026-04-09, caesar2019-nuscenes]
---

# GPS/IMU — Ego-Pose Sensors

GPS (Global Positioning System, more generally GNSS) and the IMU (Inertial Measurement Unit) are complementary sensors that together provide the **ego-vehicle pose** — the position and orientation of the car over time. In AV stacks they are almost always fused (GNSS/INS), and AV datasets like [[kitti]], [[nuscenes]], and [[waymo-open-dataset]] expose the fused output as ground-truth trajectory.

## The two sensors

### IMU — Inertial Measurement Unit
- **What it measures:** specific force (3-axis accelerometer) and angular rate (3-axis gyroscope); sometimes a 3-axis magnetometer as well (then called a 9-DoF IMU).
- **Rate:** high, typically 100–1000 Hz.
- **Strengths:** captures fast ego-motion; works everywhere regardless of sky visibility.
- **Weaknesses:** integrates to drift — position error grows unbounded without external correction.

### GPS / GNSS
- **What it measures:** absolute geodetic position via satellite trilateration; velocity from Doppler.
- **Rate:** low, typically 1–10 Hz.
- **Strengths:** bounded absolute error; drift-free over long horizons.
- **Weaknesses:** noisy (meters for consumer receivers; centimeters with RTK/PPP); degraded or lost in tunnels, urban canyons, and multipath environments.

### Fusion (GNSS/INS)
The two are complementary: GPS anchors IMU drift, and IMU bridges GPS dropouts with high-rate pose between fixes. A filter — classically an Extended Kalman Filter or error-state Kalman Filter — propagates state at IMU rate and corrects it with GPS updates. Production AV stacks typically layer additional signals (wheel odometry, HD-map matching, LiDAR odometry) on top, so the "pose" exposed by a dataset is usually a multi-sensor localization solution, not raw GPS/IMU.

## Why they matter for AV simulation

Neural scene reconstruction methods — both [[3d-gaussian-splatting]] and NeRF-based systems like [[neurad]] — require **accurate per-frame camera poses** to optimize their scene representation. Pose error propagates directly into reconstruction quality: small errors blur Gaussians / NeRF densities, and large errors prevent convergence. GPS/IMU is the upstream origin of those poses in captured driving data, so dataset pose quality is a silent but load-bearing input to every system in this wiki.

## Role in AV datasets we use

- [[kitti]] ships raw GPS/IMU as part of its sensor suite.
- [[nuscenes]] provides raw GPS/IMU (0.2° heading, 0.1° roll/pitch, 20 mm RTK, 1000 Hz) but actual localization uses Monte Carlo Localization from LiDAR + odometry against an HD map, achieving ≤10 cm error — much more robust than raw GPS/IMU ([[caesar2019-nuscenes]]).
- [[waymo-open-dataset]] provides fused ego-pose per frame; the underlying GPS/IMU streams are not typically the primary interface.

## Relevance to this wiki's focus

GPS/IMU is **not a simulation target** in the same way camera, [[lidar-simulation|LiDAR]], and radar are. You don't usually need to *synthesize* IMU data — you need to *consume* accurate real poses so that neural reconstruction can train. Two caveats:

- Closed-loop simulation ([[unisim]], [[splatad]]) needs a simulated ego-pose stream as the virtual driver steers through the reconstructed scene. This is typically dead-reckoned from vehicle dynamics, not a simulated IMU.
- Training or evaluating models that directly consume IMU (e.g., visual-inertial odometry) would require a synthetic IMU stream. No source currently in the wiki addresses this.

## Open questions

> **Open question:** How sensitive is [[splatad]] / [[neurad]] reconstruction quality to pose noise from GPS/IMU? The wiki has no quantitative study on this yet.

> **Open question:** Does any neural reconstruction method in this wiki explicitly emit synthetic IMU output for downstream VIO-style consumers? Not covered by any ingested source.

## Related
- [[kitti]], [[nuscenes]], [[waymo-open-dataset]] — all provide ego-pose derived from GPS/IMU
- [[3d-gaussian-splatting]], [[neurad]], [[splatad]] — consume these poses during training
- [[av-sim-framework]] — step 1 (reconstruction) depends on pose quality
- [[claude-gps-imu-discussion-2026-04-09]] — provenance source for this page (internal Claude session, not an external citation)
