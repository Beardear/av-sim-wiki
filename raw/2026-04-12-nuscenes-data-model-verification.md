# nuScenes Data Model — Empirical Verification Session

**Date:** 2026-04-12
**Model:** Claude Opus 4.6 (1M context), Claude Code v2.1.98
**Format:** Structured session summary — *not* a verbatim transcript.
**Predecessor:** [`2026-04-11-claude-nuscenes-lidar-discussion.md`](./2026-04-11-claude-nuscenes-lidar-discussion.md) — the earlier session that introduced the "Data Model" section of `pages/entities/nuscenes.md`. This file extends that work with empirically verified counts and a phase-mapping analysis.

## Scope

The user asked for a walkthrough of the nuScenes dataset with explicit annotation of which parts are needed for each phase of `/workspace/PROGRESS.md`'s roadmap. The session then verified every quantitative claim by loading the on-disk metadata JSONs at `/workspace/SplatAD/data/nuscenes/v1.0-trainval/` and counting directly, rather than citing paper figures. A number of imprecise claims from an initial pass were corrected against the verified counts.

## Empirically verified counts (v1.0-trainval, from disk, 2026-04-12)

All counts below were produced by loading each JSON and calling `len()`. They reflect the **trainval split specifically**, not the full 1000-scene nuScenes release.

| Table | Rows | Notes |
|---|---:|---|
| `scene.json` | **850** | Full release has 1000; 150 are held out in `v1.0-test/` |
| `sample.json` | **34,149** | 2 Hz keyframes |
| `sample_data.json` | **2,631,083** | One row per sensor-reading blob (keyframes + sweeps) |
| `ego_pose.json` | **2,631,083** | Exactly 1:1 with `sample_data` — every reading gets its own pose |
| `calibrated_sensor.json` | **10,200** | Exactly 850 × 12 — per-**scene** × sensor, *not* per-log |
| `sensor.json` | 12 | The 12 channels |
| `sample_annotation.json` | **1,166,187** | Avg ~34 boxes per keyframe |
| `instance.json` | **64,386** | Unique tracked actors across trainval — Phase 3 actor budget |
| `category.json` | **32** | Only 23 are used as annotation classes; the other 9 are lidarseg-only surface labels (`flat.driveable_surface`, `static.vegetation`, etc.) |
| `attribute.json` | 8 | |
| `visibility.json` | 4 | 4 visibility buckets |
| `log.json` | 68 | |
| `map.json` | 4 | 4 HD-map regions |
| `lidarseg.json` | 34,149 | One pointer row per keyframe |

### Structural invariants that emerged from the counts

1. **`ego_pose` is 1:1 with `sample_data`** (both 2,631,083). Every individual sensor reading gets its own pose record. There is no per-sample shared pose — the per-reading granularity is what enables SplatAD's rolling-shutter-aware LiDAR rasterizer to interpolate a fresh pose per sweep.

2. **`calibrated_sensor` is per (scene × sensor)**: 850 × 12 = 10,200. Even though a given vehicle's calibration probably doesn't change scene-to-scene within a single log, nuScenes records it per scene anyway. This corrects an initial imprecise framing in the session that suggested "per (sensor × log)" — the numbers don't support that.

3. **Every keyframe has exactly 12 `sample_data` rows** (one per channel): 34,149 × 12 = 409,788 keyframe sample_data records. Verified by picking one sample token and counting matching `is_key_frame=True` rows broken down by channel — got exactly 1 per channel.

4. **The `samples/` vs. `sweeps/` directory layout ↔ `is_key_frame` boolean is exact**, with zero cross-contamination:
   - `is_key_frame=True` (409,788 rows) → **all** under `samples/`, 0 under `sweeps/`.
   - `is_key_frame=False` (2,221,295 rows) → **all** under `sweeps/`, 0 under `samples/`.

5. **Nearly every keyframe has annotations, but not all**: 34,140 of 34,149 keyframes (99.97%) have at least one 3D box. **9 keyframes have zero annotations** — a small edge case worth knowing for Phase 3 filtering code.

6. **Zero stray annotations**: every `sample_annotation.sample_token` resolves to a real `sample` row.

### Per-channel `sample_data` breakdown (trainval)

```
LIDAR_TOP            331,886
CAM_FRONT         ≈ 198,000  (×6 cameras ≈ 1.18M total camera images)
RADAR_*           ≈ 220,000  (×5 radars ≈ 1.12M total radar blobs)
                  ─────────
sample_data total  2,631,083
```

File extensions: cameras are `.jpg`, LiDAR is `.pcd.bin`, radar is `.pcd`.

## Correction: `samples/` is not an "annotated" directory

An initial framing of `samples/` as "annotated keyframes" conflated two different things. The precise framing is:

- `samples/` is a **blob directory** holding keyframe sensor files (12 per keyframe).
- `sweeps/` is a **blob directory** holding intermediate sensor files (12/13/20 Hz).
- **Neither directory carries annotations.** Annotations live separately in `sample_annotation.json` and reference keyframes by `sample_token`.

Because 99.97% of keyframes are annotated, the shortcut "`samples/` corresponds to annotated timestamps" is *useful*, but saying "`samples/` is annotated" conflates the blob directory with the annotation table.

## Verified: `v1.0-mini` includes scene-0796

`v1.0-mini/` has 10 scenes with identical schema to trainval. Confirmed by reading `v1.0-mini/scene.json` — the 10 scenes are 0061, 0103, 0553, 0655, 0757, **0796**, 0916, 1077, 1094, 1100. Scene-0796 is the user's primary Phase 1 training target, so the mini split can be used as a **fast-iteration learning environment** (loads in <10 s vs. 5–15 min for trainval) with no schema or target-scene compromise.

## Phase → data mapping (`/workspace/PROGRESS.md`)

This is the "which tables do I actually open" decision, separate from the "which paper specs do I care about" sensor-suite material already in `pages/entities/nuscenes.md`.

| Component | Phase 1 (Domain Gap) | Phase 2 (LiDAR Realism) | Phase 3 (Scene Editing) | Notes |
|---|:---:|:---:|:---:|---|
| `samples/CAM_*` + `sweeps/CAM_*` (~1.18M files) | Essential | Useful | Essential | Training input + novel-view eval |
| `samples/LIDAR_TOP` + `sweeps/LIDAR_TOP` (331,886 files) | Essential | Essential | Essential | Phase 2 is *all* about this layer |
| `samples/RADAR_*` + `sweeps/RADAR_*` (~1.12M files, ~6 GB) | Not used | Not used | Not used | SplatAD doesn't render radar; only the radar calibration in `calibrated_sensor` is consumed |
| `scene.json`, `sample.json`, `sample_data.json`, `ego_pose.json`, `calibrated_sensor.json`, `sensor.json` | Essential | Essential | Essential | The minimum viable metadata set to walk the data |
| `sample_annotation.json` (557 MB) | Conditional | Conditional | Essential | Phase 1/2 need it only for **detector-based** sim-to-real comparisons (train CenterPoint / BEVFormer, score AP). Phase 3 needs it structurally — it is the source of truth for which actors to carve out |
| `instance.json` (64,386 unique) | Conditional | Not required | Essential | The `instance_token` is what SplatAD keys its per-actor Gaussian `id` on |
| `category`, `attribute`, `visibility` (44 rows total) | Optional | Optional | Useful | Filtering during edit harmonization |
| `log.json`, `map.json` | Not used | Not used | Not used | Pure provenance; SplatAD's dataparser doesn't read them |
| `lidarseg.json` + `lidarseg/` blobs | Not used | Useful | Not used | Could inform a material-aware intensity model in Phase 2 |
| `maps/` (raster) + `expansion/` (vector) | Not used | Not used | Useful | Only if Phase 3 grows lane-aware insertion or BEV viz |

**Phase 1 minimum on-disk set:** all camera + LiDAR blobs + 6 core metadata tables. That's roughly ~290 GB of the ~314 GB on disk.

**Phase 1 + downstream AP:** also `sample_annotation.json` (557 MB) to feed the detector at eval time.

**Phase 2 adds nothing on disk.** The improvement is *in the model* — ray-drop MLP, material-aware intensity. Optionally pull in `lidarseg/` blobs if going material-aware.

**Phase 3 adds nothing on disk beyond Phase 1 + annotations.** The change is that **`instance_token`** becomes load-bearing — Phase 3 groups annotations by `instance_token` (one of 64,386 unique identities in trainval) to drive SplatAD's per-actor `id`-tagged Gaussian sets. The neurad-studio dataparser already does this grouping at `nuscenes_dataparser.py:343` and `:402`.

## Data-literacy plan (90 min, for user, not wiki content)

The session also produced a hands-on 90-minute learning plan for developing a mental model of the nuScenes schema without memorizing the schema reference page. It uses `v1.0-mini` throughout to avoid the trainval load wait. The plan lives in `/workspace/PROGRESS.md` under Phase 1 and is considered project/pedagogy content, not wiki domain material. **Not ingested into wiki pages.**

## Operational (not wiki content)

A ~294.5 GB disk cleanup was performed on 2026-04-12 — the 11 nuScenes original `.tgz` tarballs and the `.tar.bz2` / `.zip` archives were deleted after verifying the extracted `samples/` / `sweeps/` / `v1.0-trainval/` contents. Recorded in `/workspace/PROGRESS.md` Environment section. **Not wiki material.**

## Notes on reliability

- The row counts, directory ↔ `is_key_frame` mapping, per-sample channel distribution, and 99.97% annotation-rate figure were **all empirically verified** by loading the on-disk JSONs in Python. These are not recollections.
- The claim "`calibrated_sensor` is per (scene × sensor), not per (sensor × log)" is derived from the 10,200 = 850 × 12 arithmetic and the absence of a `log_token` or `scene_token` field in the schema. This should be confirmed by reading the nuScenes-devkit source or the official documentation if anyone downstream depends on it.
- The "Phase → data mapping" table is a judgment call rooted in PROGRESS.md's Phase 1/2/3 goals and in reading `nuscenes_dataparser.py`. It is **not** from the nuScenes paper — it reflects how SplatAD *uses* the dataset, not how nuScenes was designed.
- The claim that `nuscenes_dataparser.py:343` contains the `instance_token` grouping is verified via Grep in this session (confirmed at lines 343 and 402 of the 451-line file).
- The paper-sourced claims ("1.4M images", "1000 scenes", sensor specs) already in `pages/entities/nuscenes.md` remain correct for the **full release**; this session adds the **trainval-specific** counts as a separate layer, not a replacement.

## Open questions

> **Open question:** Is `calibrated_sensor` truly per-scene, or does nuScenes sometimes issue multiple `calibrated_sensor` rows for the same (scene, sensor) pair when calibration changes mid-sequence? The 10,200 = 850 × 12 identity strongly suggests one row per pair, but the schema technically allows more. Verify via the devkit documentation before building Phase 3 code that assumes uniqueness.

> **Open question:** What causes the 9 keyframes with zero annotations in trainval? Empty intersections in rural segments? Annotator gaps? Worth logging their sample tokens for Phase 3 filter tests.

> **Open question:** Does the SplatAD CVPR 2025 paper's rolling-shutter-aware rasterizer (see `claude-nuscenes-lidar-discussion-2026-04-11`) actually pull from `ego_pose.json` at the 1:1 `sample_data` granularity, or does it interpolate to an even finer per-point timestamp? The 1:1 invariant observed here is necessary but not sufficient to answer this.
