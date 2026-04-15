---
title: nuScenes
type: entity
created: 2026-04-07
updated: 2026-04-12
tags: [dataset, multi-sensor, benchmark]
sources: [caesar2019-nuscenes, claude-nuscenes-lidar-discussion-2026-04-11, claude-nuscenes-data-model-discussion-2026-04-12, gemini-depth-maps-datasets, gemini-radar-doppler-dynamic-gs]
---

# nuScenes

## What It Is
A large-scale autonomous driving dataset collected in Boston and Singapore by Motional (formerly nuTonomy). The de facto standard benchmark for multi-sensor AV perception.

## Sensor Suite

Two identical Renault Zoe supermini electric cars with the same sensor layout (one Boston, one Singapore). See [[caesar2019-nuscenes]] Table 2 and Figure 4.

### Cameras (6×)
RGB, 12 Hz, 1/1.8" CMOS, 1600×900, auto exposure, JPEG.

| Channel | Direction | FOV |
|---------|-----------|-----|
| `CAM_FRONT` | Front-center | 70° |
| `CAM_FRONT_LEFT` | Front-left, offset 55° from front | 70° |
| `CAM_FRONT_RIGHT` | Front-right, offset 55° from front | 70° |
| `CAM_BACK` | Rear-center | 110° (wider) |
| `CAM_BACK_LEFT` | Rear-left | 70° |
| `CAM_BACK_RIGHT` | Rear-right | 70° |

### LiDAR (1×)
`LIDAR_TOP` — roof-mounted spinning, 32 beams, 20 Hz, 360° H-FOV, −30° to +10° V-FOV, ≤70 m range, ±2 cm accuracy, up to 1.4M pts/s.

### Radars (5×)
77 GHz FMCW, 13 Hz, ≤250 m range, ±0.1 km/h velocity accuracy. No `RADAR_BACK` — rear covered by two back-corner radars.

| Channel | Direction |
|---------|-----------|
| `RADAR_FRONT` | Front bumper |
| `RADAR_FRONT_LEFT` | Front-left corner |
| `RADAR_FRONT_RIGHT` | Front-right corner |
| `RADAR_BACK_LEFT` | Rear-left corner |
| `RADAR_BACK_RIGHT` | Rear-right corner |

### GPS/IMU
0.2° heading, 0.1° roll/pitch, 20 mm RTK positioning, 1000 Hz. However, actual localization uses Monte Carlo Localization from LiDAR + odometry against an HD map, achieving ≤10 cm error — much more robust than raw [[gps-imu|GPS/IMU]].

### Coordinate frames
Cameras use the CV convention (X-right, Y-down, Z-forward); LiDAR/radar/ego use the robotics convention (X-forward, Y-left, Z-up). See [[sensor-coordinate-frames]].

### Sensor synchronization
Camera exposure triggered when the LiDAR beam sweeps across each camera's FOV center. 12 camera exposures spread across 20 LiDAR scans per second.

## Scale
- 1000 scenes total, each 20 seconds (5.5 h total, 242 km at avg 16 km/h) — split into **850 trainval** + **150 test** (held out, metadata-only; no annotations released)
- 1.4M camera images, 400K LiDAR sweeps, 1.3M radar sweeps
- 1.4M 3D bounding box annotations across 23 object classes with 8 attributes
- Key frames at 2 Hz (with full annotations); intermediate frames at 12/13/20 Hz (camera/radar/LiDAR)
- Conditions: rain 19.4%, night 11.6%
- Locations: Boston-Seaport 55%, SG-OneNorth 21.5%, SG-Queenstown 13.5%, SG-HollandVillage 10%

## Data Model — Samples, Sweeps, and Tokens

nuScenes organizes data along two orthogonal axes: (1) the **samples vs. sweeps** distinction, which controls whether a sensor reading has full annotations; and (2) a **token-keyed relational schema** that links scenes, sensor readings, annotations, and ego-poses.

### Samples vs. Sweeps

- **`samples/`** — a **blob directory** holding the keyframe sensor files captured at 2 Hz. All sensors are time-synchronized at each keyframe, and the corresponding 3D box annotations live in `sample_annotation.json` (a separate table), linked back by `sample_token`. The directory itself carries **no** annotation data — only the raw files. It is convenient shorthand to call `samples/` "the annotated frames" because 99.97% of keyframes are annotated (verified below), but the annotation table and the blob directory are structurally distinct.
- **`sweeps/`** — all other sensor readings at the native sensor rate: cameras ~12 Hz, `LIDAR_TOP` 20 Hz, radars 13 Hz. Unlabeled, but the raw sensor data is identical in quality to the keyframe data.

Scenes are **not** stored as separate folders. Files are organized by sensor (`samples/CAM_FRONT/`, `sweeps/LIDAR_TOP/`, ...) and scene membership is resolved via metadata tokens. SplatAD and other 3DGS systems use sweeps heavily — keyframe-only training would give ~5–10× fewer views per scene and would undersample the LiDAR temporal dimension, which matters for [[lidar-motion-compensation|rolling-shutter modeling]] and dynamic-actor reconstruction.

### Token-Keyed Relational Schema

Metadata lives in a set of JSON files under `v1.0-trainval/` (or `v1.0-mini/`, `v1.0-test/`) that behave like tables in a relational database. Each row has a 32-character hex `token` primary key, and rows reference each other by token — every field ending in `_token` is a cross-reference. The nuscenes-devkit loads all JSONs on import and builds O(1) in-memory hash tables for lookups. This hash-table build is part of why launching a single-scene SplatAD training takes several minutes before the first step (see also [[lidar-motion-compensation]] for the LiDAR preprocessing contribution).

Key tables and what they hold:

| Table | Contents | Why it matters |
|-------|----------|----------------|
| `scene.json` | 20-second driving sequences, location, first/last sample | Selection unit at train time (`--sequence 0796`) |
| `sample.json` | Keyframes at 2 Hz, with `prev`/`next` chain | Defines what gets annotated |
| `sample_data.json` | One row per sensor reading file (sample **and** sweep), `is_key_frame` flag | Where raw files and sensor timestamps live |
| `sample_annotation.json` | One row per 3D cuboid at a keyframe, linked to an `instance_token` | Detection/tracking ground truth |
| `instance.json` | One row per physical object observed in the dataset | Stable cross-time identity |
| `ego_pose.json` | `(timestamp, translation, rotation)` at ~100 Hz | Source of per-time ego pose for motion compensation |
| `calibrated_sensor.json` | Fixed sensor mounting per scene | `T_ego_sensor` extrinsics |
| `category.json`, `attribute.json`, `visibility.json` | Lookup tables for annotation metadata | Filtering / classification |

### `instance_token` and Free Tracks

The most important token for [[scene-editing|Phase 3 scene editing]] work is `instance_token`. An *instance* is one physical object observed across time. If the same car appears in 20 consecutive keyframes, there are 20 `sample_annotation` rows but they all share the same `instance_token`. That stable identity gives you **object tracks for free**, without running a tracker — just `WHERE instance_token = X`.

[[splatad]]'s dynamic actor decomposition uses `id = instance_token` as the tag on per-actor Gaussian sets. This is the structural hook that makes actor removal / insertion possible: annotations stop being merely evaluation data and become an input to reconstruction.

### Annotations Across Phases

Annotations (`sample_annotation` + `instance` + `lidarseg/`) are used across the project's phased roadmap. See [[sim-to-real-transfer]] for context.

- **Phase 1 & Phase 2 (symmetric use):** any **detector-based** sim-to-real comparison — "run [[centerpoint]] on sim vs real, compare AP" — requires cuboids both to train the detector (it is supervised on `sample_annotation`) and to score its output (AP needs GT boxes to match against). This is the punchline metric of both phases. By contrast, PSNR/SSIM/LPIPS/FID (images) and Chamfer/intensity/ray-drop (LiDAR geometry) do **not** need annotations at all.
- **Phase 3 (structural use):** [[splatad]] dynamic-actor training needs the cuboids themselves as input to carve per-actor Gaussian sets, plus `instance_token` for cross-frame linkage, plus interpolated cuboid poses from 2 Hz up to sweep rate.

## v1.0-trainval — Empirically Verified Counts (2026-04-12)

All counts below were produced by loading the metadata JSONs at `/workspace/SplatAD/data/nuscenes/v1.0-trainval/` with `json.load()` and calling `len()`. They reflect the **trainval split specifically** and complement — not replace — the full-release paper figures in the [Scale](#scale) section. See [[claude-nuscenes-data-model-discussion-2026-04-12]] for provenance.

| Table | Rows (trainval) | Notes |
|---|---:|---|
| `scene.json` | **850** | Full release has 1000; 150 held out in `v1.0-test/` |
| `sample.json` | **34,149** | 2 Hz keyframes |
| `sample_data.json` | **2,631,083** | One row per sensor-reading blob (keyframes **and** sweeps). The paper's "1.4M images" figure is camera-only. |
| `ego_pose.json` | **2,631,083** | **Exactly 1:1 with `sample_data`** — every individual reading gets its own pose record, not one per keyframe |
| `calibrated_sensor.json` | **10,200** | **Exactly 850 × 12** — one row per `(scene, sensor)` pair. Not per-log, not per-vehicle. |
| `sample_annotation.json` | **1,166,187** | Avg ~34 boxes per keyframe; the paper's "1.4M" figure is the full release |
| `instance.json` | **64,386** | Unique tracked actors across trainval — this is the Phase 3 "how many actors am I editing" budget |
| `category.json` | **32** | Only **23** are used as annotation classes; the other 9 are lidarseg-only surface labels (`flat.driveable_surface`, `static.vegetation`, etc.) |
| `lidarseg.json` | 34,149 | Pointer row per keyframe into `lidarseg/` blobs |

### Structural invariants surfaced by the counts

- **`samples/` vs. `sweeps/` ↔ `is_key_frame` is a bijection.** 409,788 `sample_data` rows have `is_key_frame=True` — **all** of them under `samples/`, zero under `sweeps/`. 2,221,295 rows have `is_key_frame=False` — **all** under `sweeps/`, zero under `samples/`. The directory name encodes the flag losslessly.
- **Every keyframe has exactly 12 `sample_data` rows**, one per channel: 34,149 × 12 = 409,788.
- **Nearly every keyframe is annotated, but not all**: 34,140 of 34,149 keyframes (99.97%) carry ≥1 box. **9 keyframes have zero annotations** — a small edge case worth handling in Phase 3 filter code.
- **`ego_pose` is 1:1 with `sample_data`**, not per-keyframe. This granularity is what [[splatad]]'s rolling-shutter-aware LiDAR rasterizer feeds on — see [[lidar-motion-compensation]] for why per-sweep (and ideally per-point) poses matter.
- **`calibrated_sensor` is per-scene**, not per-log. Even though calibration presumably doesn't change scene-to-scene within a log, nuScenes records one row per `(scene, sensor)` pair anyway.

### Per-channel `sample_data` breakdown

```
LIDAR_TOP            331,886
CAM_FRONT         ≈ 198,000   (×6 cameras ≈ 1.18M total camera images)
RADAR_*           ≈ 220,000   (×5 radars ≈ 1.12M total radar blobs)
                  ─────────
sample_data total  2,631,083  = 409,788 keyframes + 2,221,295 sweeps
```

File extensions: cameras are `.jpg`, LiDAR is `.pcd.bin`, radar is `.pcd`.

### `v1.0-mini` as a fast-iteration learning environment

`v1.0-mini/` has 10 scenes with identical schema to trainval but loads in <10 s vs. 5–15 min for trainval (observed in `/workspace/devlog.md`, 2026-04-11). Verified by reading `v1.0-mini/scene.json`, the 10 mini scenes are **0061, 0103, 0553, 0655, 0757, 0796, 0916, 1077, 1094, 1100** — notably including **scene-0796**, which is the user's primary Phase 1 training target. This makes `v1.0-mini` viable for hands-on devkit exploration without sacrificing the target scene, and is the recommended substrate for schema-learning exercises.

## Phase → Data Mapping

Which tables and blob directories each phase of `/workspace/PROGRESS.md` actually opens. This is the "which file do I need" decision, distinct from "which paper specs do I care about."

| Component | Phase 1 (Domain Gap) | Phase 2 (LiDAR Realism) | Phase 3 (Scene Editing) | Notes |
|---|:---:|:---:|:---:|---|
| `samples/CAM_*` + `sweeps/CAM_*` (~1.18M files) | Essential | Useful | Essential | Training input + novel-view eval |
| `samples/LIDAR_TOP` + `sweeps/LIDAR_TOP` (331,886 files) | Essential | Essential | Essential | Phase 2 is *all* about this layer |
| `samples/RADAR_*` + `sweeps/RADAR_*` (~1.12M files, ~6 GB) | Not used | Not used | Not used | [[splatad]] doesn't render radar; only the radar *calibration* in `calibrated_sensor` is consumed |
| `scene`, `sample`, `sample_data`, `ego_pose`, `calibrated_sensor`, `sensor` | Essential | Essential | Essential | The minimum viable metadata set to walk the data |
| `sample_annotation` (557 MB) | Conditional | Conditional | Essential | Phase 1/2 need it only for **detector-based** sim-to-real comparisons (train + score [[centerpoint]] / [[bevformer]]). Phase 3 needs it as the source of truth for which actors to carve |
| `instance` (64,386 unique) | Conditional | Not used | Essential | The `instance_token` is what [[splatad]] keys its per-actor Gaussian `id` on |
| `category`, `attribute`, `visibility` (44 rows) | Optional | Optional | Useful | Filtering during edit harmonization |
| `log`, `map` | Not used | Not used | Not used | Pure provenance; SplatAD's dataparser doesn't read them |
| `lidarseg.json` + `lidarseg/` blobs | Not used | Useful | Not used | Could inform a material-aware intensity model in Phase 2 |
| `maps/` raster + `expansion/` vector | Not used | Not used | Useful | Only if Phase 3 grows into lane-aware insertion or BEV viz |

**Phase 1 adds nothing that isn't already essential** — the bottleneck is training runs and eval infrastructure, not data coverage.

**Phase 2 adds nothing on disk.** The improvement lives in the model (ray-drop MLP, material-aware intensity). Optionally pull `lidarseg/` blobs if going material-aware.

**Phase 3 adds nothing on disk beyond Phase 1 + annotations.** The change is that **`instance_token`** becomes load-bearing — Phase 3 groups annotations by `instance_token` (one of 64,386 unique identities) to drive SplatAD's per-actor `id`-tagged Gaussian sets. The neurad-studio dataparser already does this grouping at `nuscenes_dataparser.py:343,402`.

## Why It's Our Primary Dataset
- Multi-sensor (camera + LiDAR + radar) — matches our simulation target
- Well-supported by neurad-studio dataparser
- Large enough for meaningful evaluation
- Diverse conditions: day, night, rain, intersections, highways
- Strong baseline results from many perception models ([[centerpoint|CenterPoint]], [[bevformer|BEVFormer]], etc.)

## nuScenes for Simulation
- Train [[splatad]] per-scene on nuScenes data
- Render novel views for all 6 cameras + LiDAR
- Compare rendered vs. real for domain gap measurement
- Use nuScenes detection benchmark to measure perception-level gap

## Metrics Defined by nuScenes

- **NDS** (nuScenes Detection Score): (1/10) × [5 × mAP + Σ(1 − min(1, mTP))]. Combines mAP with five TP quality metrics (ATE, ASE, AOE, AVE, AAE).
- **Center-distance matching** at thresholds {0.5, 1, 2, 4} m instead of IOU — better for small objects.
- **sAMOTA**: tracking metric extending AMOTA with recall adjustment.

See [[caesar2019-nuscenes]] for full metric definitions and baseline results.

## Sensor Rig Config
Need to create a YAML config for virtual-sensor-suite matching nuScenes geometry:
- 6 cameras with known intrinsics and extrinsics (from `calibrated_sensor` table)
- LiDAR (32-beam spinning) with elevation angles
- 5 radars with positions and orientations
- Must handle [[sensor-coordinate-frames|coordinate frame]] differences between sensor types

> **Open question:** Which nuScenes scenes are best for initial SplatAD training? Want diversity: day/night, rain/clear, intersection/highway.

> **Open question:** How does nuScenes' ≤10 cm MCL localization accuracy affect [[splatad]] reconstruction quality compared to [[waymo-open-dataset]]?

## Dataset Comparison
| Feature | [[nuscenes]] | [[kitti]] | [[waymo-open-dataset]] | [[argoverse]] |
|---------|------------|---------|---------------------|-------------|
| Cameras | 6 (360-deg) | 2 stereo pairs (front) | 5 | Surround |
| LiDAR | 32-beam | 64-beam | 5 sensors | 310m range |
| Radar | 5 radars | None | No public | None |
| Conditions | Day/night/rain | Day only | Day/night | Urban US |

## Related
- [[splatad]] — trains on nuScenes
- [[kitti]] — simpler dataset used for proof-of-concept
- [[waymo-open-dataset]] — comparable large-scale multi-sensor AV dataset
- [[argoverse]] — long-range LiDAR alternative
- [[radar-simulation]] — nuScenes is the primary dataset with radar data
- [[sensor-fusion]] — nuScenes multi-modal data enables fusion research
- [[mmdetection3d]] — standard framework for nuScenes benchmarks
- [[sim-to-real-transfer]] — nuScenes as evaluation benchmark
- [[sensor-coordinate-frames]] — coordinate conventions across sensor types
- [[lidar-motion-compensation]] — `ego_pose.json` is the source of per-time poses for LiDAR unwarping / rolling-shutter-aware rendering
- [[caesar2019-nuscenes]] — primary source paper
- [[claude-nuscenes-lidar-discussion-2026-04-11]] — provenance for the data model / token section
- [[claude-nuscenes-data-model-discussion-2026-04-12]] — provenance for the empirically verified row counts, structural invariants, and phase-mapping table
