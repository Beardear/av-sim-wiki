---
title: "Caesar et al. 2019 — nuScenes: A Multimodal Dataset for Autonomous Driving"
type: source
created: 2026-04-11
updated: 2026-04-11
tags: [dataset, multi-sensor, benchmark, detection, tracking, lidar, radar, camera]
sources: [caesar2019-nuscenes]
---

# Caesar et al. 2019 — nuScenes

**Full citation:** Holger Caesar, Varun Bankiti, Alex H. Lang, Sourabh Vora, Venice Erin Liong, Qiang Xu, Anush Krishnan, Yu Pan, Giancarlo Baldan, Oscar Beijbom. "nuScenes: A Multimodal Dataset for Autonomous Driving." CVPR 2020 (arXiv:1903.11027v5, May 2020). nuTonomy / APTIV.

**Raw source:** `raw/caesar2019-nuscenes.pdf`

## Key Contributions

1. **First full-suite AV dataset**: 6 cameras + 1 LiDAR + 5 radars with 360° coverage — the first dataset to include radar and the full sensor suite from a public-road-approved AV.
2. **Scale**: 1000 scenes (20s each, 5.5h total), 1.4M camera images, 400K LiDAR sweeps, 1.3M radar sweeps, 1.4M 3D bounding box annotations across 23 classes with 8 attributes. Boston + Singapore.
3. **Novel metrics**: nuScenes Detection Score (NDS) combining mAP with TP metrics (ATE, ASE, AOE, AVE, AAE); center-distance matching instead of IOU to decouple size/orientation; sAMOTA for tracking.
4. **Sensor synchronization**: Camera exposure triggered by LiDAR sweep crossing camera FOV center — ensures good cross-modality alignment without explicit hardware sync.
5. **Localization**: Monte Carlo Localization from LiDAR + odometry against HD map (≤10 cm error), significantly more robust than raw GPS/IMU used by [[kitti]].

## Sensor Suite — Detailed Specifications

### Sensor channels (from devkit)

| Channel | Type | Placement |
|---------|------|-----------|
| `CAM_FRONT` | Camera | Front-center, 70° FOV |
| `CAM_FRONT_LEFT` | Camera | Front-left, 70° FOV, offset 55° from front |
| `CAM_FRONT_RIGHT` | Camera | Front-right, 70° FOV, offset 55° from front |
| `CAM_BACK` | Camera | Rear-center, 110° FOV (wider) |
| `CAM_BACK_LEFT` | Camera | Rear-left, 70° FOV |
| `CAM_BACK_RIGHT` | Camera | Rear-right, 70° FOV |
| `LIDAR_TOP` | LiDAR | Roof-mounted, 360° horizontal, −30° to +10° vertical |
| `RADAR_FRONT` | Radar | Front bumper area |
| `RADAR_FRONT_LEFT` | Radar | Front-left corner |
| `RADAR_FRONT_RIGHT` | Radar | Front-right corner |
| `RADAR_BACK_LEFT` | Radar | Rear-left corner |
| `RADAR_BACK_RIGHT` | Radar | Rear-right corner |

### Sensor specifications (paper Table 2)

| Sensor | Details |
|--------|---------|
| 6× Camera | RGB, 12 Hz, 1/1.8" CMOS, 1600×900, auto exposure, JPEG |
| 1× LiDAR | Spinning, 32 beams, 20 Hz, 360° H-FOV, −30° to +10° V-FOV, ≤70 m range, ±2 cm accuracy, up to 1.4M pts/s |
| 5× Radar | 77 GHz FMCW, 13 Hz, ≤250 m range, ±0.1 km/h velocity accuracy |
| GPS/IMU | 0.2° heading, 0.1° roll/pitch, 20 mm RTK positioning, 1000 Hz |

Note: no `RADAR_BACK` channel — rear coverage comes from the two back-corner radars.

### Coordinate frames

Each sensor type uses a different native coordinate convention. See [[sensor-coordinate-frames]] for details. Key point: cameras use the computer-vision convention (X-right, Y-down, Z-forward) while LiDAR, radar, and the ego vehicle use the robotics convention (X-forward, Y-left, Z-up). The `calibrated_sensor` table stores rotation + translation extrinsics that bridge these frames.

### Sensor synchronization

Camera exposures are triggered when the spinning LiDAR beam sweeps across the center of each camera's FOV. Since cameras run at 12 Hz and LiDAR at 20 Hz, the 12 exposures are spread as evenly as possible across the 20 LiDAR scans (not every LiDAR scan has a corresponding camera frame).

## Data Collection

- **Vehicles**: Two identical Renault Zoe supermini electric cars (one Boston, one Singapore).
- **Driving**: 84 logs, 15 h total, 242 km at avg 16 km/h. Routes chosen for diversity: urban, residential, nature, industrial; day/night; sun/rain/cloud.
- **Scene selection**: 1000 scenes manually picked from raw data, emphasizing high traffic density, rare classes, dangerous situations, diverse maneuvers.
- **Annotations**: Keyframes at 2 Hz, 23 object classes, 3D cuboid (x, y, z, w, l, h, yaw) + attributes (visibility, activity, pose). Intermediate sensor frames also released (12/13/20 Hz for camera/radar/LiDAR).
- **Conditions**: rain 19.4%, night 11.6%. Locations: Boston-Seaport 55%, SG-OneNorth 21.5%, SG-Queenstown 13.5%, SG-HollandVillage 10%.

## Detection & Tracking Metrics

### NDS (nuScenes Detection Score)
NDS = (1/10) × [5 × mAP + Σ(1 − min(1, mTP))]

Half of NDS comes from mAP, the other half from five True Positive quality metrics:
- **mATE** — Average Translation Error (2D Euclidean, meters)
- **mASE** — Average Scale Error (1 − IOU after alignment)
- **mAOE** — Average Orientation Error (yaw, radians)
- **mAVE** — Average Velocity Error (L2 norm, m/s)
- **mAAE** — Average Attribute Error (1 − accuracy)

Uses center-distance matching (thresholds 0.5/1/2/4 m) instead of IOU, which avoids penalizing small objects (pedestrians, bicycles) disproportionately.

### Tracking: sAMOTA
Extends AMOTA by augmenting MOTA with a recall-adjustment term to span [0,1]. Also introduces TID (Track Initialization Duration) and LGD (Longest Gap Duration).

## Key Experimental Findings

- [[pointpillars]] with 10 accumulated LiDAR sweeps reaches NDS 44.8% / mAP 28.8% (val), significantly better than single-sweep (NDS 31.8% / mAP 21.9%). Temporal accumulation helps both detection and velocity estimation.
- PointPillars (lidar) and MonoDIS (camera) achieve similar mAP (~30%), but PointPillars has higher NDS (45.3% vs 38.4%) due to better translation and velocity estimates.
- Center-distance matching changes the relative ranking of lidar vs. camera methods compared to IOU matching — especially for thin objects like bicycles.
- Pre-training (ImageNet or KITTI) has marginal impact on final PointPillars performance when training data is sufficient.
- Method ordering changes with dataset size — PointPillars separates from SSD+3D only at nuScenes-scale data, not KITTI-scale.

## Limitations and Open Questions

- **Radar underexplored**: Paper notes no competitive radar-only detection methods exist; their preliminary PointPillars-on-radar study was not promising. Radar fusion remains wide open.
- **Annotation frequency**: 2 Hz keyframes. Interpolation to higher rates (10–20 Hz) is possible but introduces smoothing artifacts.
- **Localization ceiling**: MCL achieves ≤10 cm error, which is excellent but not characterized in terms of downstream impact on neural reconstruction quality.

> **Open question:** How does nuScenes' 10 cm localization accuracy affect [[splatad]] reconstruction compared to [[waymo-open-dataset]]'s localization?

## Pages Informed by This Source

- [[nuscenes]] — primary entity page (sensor suite, scale, collection details)
- [[sensor-coordinate-frames]] — new concept page on native coordinate conventions
- [[pointpillars]] — baseline results from this paper
- [[gps-imu]] — nuScenes localization details (MCL, not raw GPS/IMU)
- [[lidar-simulation]] — LiDAR sensor specs relevant to simulation fidelity targets
- [[sim-to-real-transfer]] — NDS/mAP metrics defined here are the evaluation standard
