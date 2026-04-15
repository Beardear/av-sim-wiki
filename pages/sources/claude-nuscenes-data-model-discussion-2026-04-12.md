---
title: Claude nuScenes Data-Model Verification Session (2026-04-12)
type: source
created: 2026-04-12
updated: 2026-04-12
tags: [discussion, provenance, nuscenes, data-model, empirical, phase-mapping]
sources: [claude-nuscenes-data-model-discussion-2026-04-12]
---

# Claude nuScenes Data-Model Verification — 2026-04-12

## Citation

Internal Claude Code session. Model: **Claude Opus 4.6 (1M context)**, Claude Code v2.1.98. Session date: 2026-04-12. Notes file: [`raw/2026-04-12-nuscenes-data-model-verification.md`](../../raw/2026-04-12-nuscenes-data-model-verification.md).

**Not a peer-reviewed or externally authored source.** This page exists for provenance — it documents the second-pass empirical verification that extended the earlier 2026-04-11 session's [[nuscenes]] Data Model section with row counts and structural invariants loaded directly from the on-disk v1.0-trainval JSONs. Treat as metadata about the wiki's own construction, not as an independent citation backing any claim in adversarial contexts.

## Scope

The session had three connected goals:

1. **Walk the user through the nuScenes dataset structure** with the level of depth required for a data-sim engineering role, explicitly mapping each metadata table and blob directory to the phases of `/workspace/PROGRESS.md`'s roadmap.
2. **Verify every quantitative claim empirically** by loading the metadata JSONs at `/workspace/SplatAD/data/nuscenes/v1.0-trainval/` and counting directly. Several initially imprecise figures were corrected against the verified counts.
3. **Produce a 90-minute learning plan** for the user to develop a mental model of the schema via hands-on joins, using `v1.0-mini` to avoid the 5–15 minute trainval load time.

The domain output (items 1 and 2) is ingested into the wiki. The pedagogy output (item 3) is out of scope for the wiki and lives in `/workspace/PROGRESS.md` instead.

## Key points synthesized in the session

### Empirical row counts (v1.0-trainval, verified from disk)

All counts loaded via `json.load()` and `len()`; these are not recollections.

| Table | Rows | Structural note |
|---|---:|---|
| `scene` | 850 | Trainval only; full release is 1000 (150 held out in test) |
| `sample` | 34,149 | Keyframes at 2 Hz |
| `sample_data` | 2,631,083 | One row per sensor-reading file (keyframes + sweeps) |
| `ego_pose` | 2,631,083 | **Exactly 1:1 with `sample_data`** — per-reading pose |
| `calibrated_sensor` | 10,200 | **Exactly 850 × 12** — per-scene × sensor |
| `sample_annotation` | 1,166,187 | Avg ~34 boxes/keyframe |
| `instance` | 64,386 | Unique tracked actors in trainval |
| `category` | 32 | Only 23 used as annotation classes; 9 are lidarseg-only surface labels |

### Structural invariants

- **`samples/` vs. `sweeps/` ↔ `is_key_frame` is exact.** 409,788 keyframe sample_data rows are *all* under `samples/`, zero cross-contamination. 2,221,295 sweep rows are *all* under `sweeps/`. This bijection is worth asserting explicitly because the two directory names look arbitrary.
- **Every keyframe has exactly 12 sample_data rows**, one per channel. 34,149 × 12 = 409,788. Verified by picking one `sample_token` and counting channel distribution.
- **Nearly every keyframe is annotated, but not all:** 34,140 of 34,149 keyframes (99.97%) carry ≥1 box. **Nine keyframes have zero annotations** — an edge case Phase 3 filter code should handle.
- **`ego_pose` 1:1 with `sample_data`**: no per-sample shared pose. The per-reading granularity is what SplatAD's rolling-shutter-aware LiDAR rasterizer consumes (compare to [[claude-nuscenes-lidar-discussion-2026-04-11]]).
- **`calibrated_sensor` is per-scene, not per-log**: the 10,200 = 850 × 12 identity falsifies any framing of "one calibration record per vehicle per log." nuScenes records calibration per scene even when it probably doesn't change.

### Corrections against prior framings

1. **"`samples/` is annotated"** → **"`samples/` is a keyframe-blob directory; annotations live in `sample_annotation.json` and reference keyframes via `sample_token`."** The two are not the same thing even though 99.97% of keyframes are annotated. Conflating them obscures the Phase-3 distinction that annotations become a *structural input* to reconstruction, not just an eval target.
2. **"1000 scenes"** (the paper figure) → **"850 trainval, 150 test, 1000 full release."** Phase-1/2/3 work happens on trainval; the test split is metadata-only and exists only for the public leaderboard.
3. **"`calibrated_sensor` is per (sensor, log)"** → **"per (scene × sensor)."** The 10,200 row count makes this arithmetically unambiguous.
4. **"`sample_data` has ~1.4M records"** → **"2.63M for trainval."** The 1.4M figure from the paper refers to camera-only images, not all sensor-reading blobs.

### Phase → data mapping

The session produced an explicit mapping from each metadata table and blob category to the phases of `/workspace/PROGRESS.md` — which tables each phase actually reads, which it ignores, and why. Summary:

- **Phase 1 (Domain Gap)**: needs all 6 cameras + LiDAR blobs and 6 core metadata tables (`scene`, `sample`, `sample_data`, `ego_pose`, `calibrated_sensor`, `sensor`). `sample_annotation` only enters if measuring detector-based AP (CenterPoint / BEVFormer).
- **Phase 2 (LiDAR Realism)**: **adds nothing on disk**. The improvement lives in the model — ray-drop MLP, intensity physics. Optionally touches `lidarseg/` for material-aware intensity.
- **Phase 3 (Scene Editing)**: **adds nothing on disk beyond Phase 1 + annotations.** The change is that `instance_token` (64,386 unique identities) becomes the structural key for SplatAD's per-actor `id`-tagged Gaussian sets, and the dataparser at `nuscenes_dataparser.py:343,402` already does this grouping.
- **Not used by any phase**: radar blobs (SplatAD doesn't render radar; only the radar calibration is consumed), `log.json`, `map.json`.

Full phase-mapping table (with file counts and conditional/optional markers) is in the raw notes.

### `v1.0-mini` contains scene-0796

Verified by reading `v1.0-mini/scene.json`. The 10 mini scenes are 0061, 0103, 0553, 0655, 0757, **0796**, 0916, 1077, 1094, 1100. Scene-0796 is the user's primary Phase 1 training target, so `v1.0-mini` is viable as a fast-iteration learning environment (loads in <10 s vs. 5–15 min) with no schema or target-scene compromise. This fact makes the data-literacy exercise tractable without needing the full trainval hash-table build.

## Limitations

- **Not an external authoritative source.** This page documents the wiki's own construction; it does not validate it. Any claim traceable to this session should ultimately be confirmed against the nuScenes-devkit source, [[caesar2019-nuscenes]], or the SplatAD CVPR 2025 paper (not yet ingested).
- **Verified counts are trainval-specific.** They do not generalize to v1.0-mini or v1.0-test without re-counting. The paper-sourced figures for the full 1000-scene release remain on the primary [[nuscenes]] page as a separate layer.
- **The "per-scene, not per-log" claim for `calibrated_sensor`** is arithmetically well-supported (10,200 = 850 × 12) but has not been confirmed against the nuScenes-devkit documentation. Flagged as an open question below.
- **Self-referential.** This page was produced by the same session that wrote the derived wiki updates; it cannot validate them.
- **The pedagogy output (90-minute data-literacy plan) is not wiki material** and is not included here. It lives in `/workspace/PROGRESS.md`.

## Open questions

> **Open question:** Is `calibrated_sensor` *strictly* per-scene, or does nuScenes ever issue multiple rows for the same `(scene, sensor)` pair when calibration changes mid-sequence? The 10,200 = 850 × 12 identity strongly suggests one row per pair, but the schema technically permits more. Verify via the devkit docs before Phase 3 code assumes uniqueness.

> **Open question:** What causes the 9 keyframes with zero annotations in trainval? Empty intersections? Annotator gaps? Worth logging their sample tokens and flagging them for Phase 3 filter tests.

> **Open question:** Does the SplatAD CVPR 2025 rolling-shutter-aware rasterizer actually consume `ego_pose.json` at the 1:1 `sample_data` granularity, or does it interpolate further to a per-point timestamp? The 1:1 invariant observed here is necessary but not sufficient to answer this — cross-references [[claude-nuscenes-lidar-discussion-2026-04-11]].

## Informs

- [[nuscenes]] — expanded with a "v1.0-trainval — Empirically Verified Counts" subsection, a structural-invariants callout, a clarification that `samples/` is a blob directory (not "annotated"), and a Phase → Data mapping table.
- No new concept or entity pages created. The empirical material extends an existing primary entity page rather than adding new cross-cutting concepts.

## Related

- [[caesar2019-nuscenes]] — the authoritative source for nuScenes structure; the paper figures remain load-bearing for sensor specs and full-release counts.
- [[claude-nuscenes-lidar-discussion-2026-04-11]] — the immediate predecessor session that introduced the Data Model section of [[nuscenes]] and the [[lidar-motion-compensation]] concept page. This 2026-04-12 session extends that work empirically.
- [[claude-gps-imu-discussion-2026-04-09]] — earlier Claude-session provenance precedent; same authorship pattern.
- [[splatad]] — the primary consumer of the metadata tables; `nuscenes_dataparser.py` walks them to build SplatAD training inputs.
