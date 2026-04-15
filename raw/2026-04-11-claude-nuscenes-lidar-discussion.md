# Claude session — nuScenes data model, LiDAR rolling shutter, annotations

**Date:** 2026-04-11
**Model:** Claude Opus 4.6 (1M context), Claude Code v2.1.98
**Context:** User was updating `/workspace/devlog.md` to record that nuScenes v1.0-trainval blob extraction had finished. A follow-up thread of Q&A developed around nuScenes data layout and LiDAR handling. This file is a summary of the substantive exchanges — not a literal verbatim export.

---

## Exchange 1: "where are the scenes located"

User asked where the extracted nuScenes scenes live on disk. Claude answered:

- Extracted data under `/workspace/SplatAD/data/nuscenes/`
- Structure:
  - `samples/` — keyframe sensor data
  - `sweeps/` — intermediate non-keyframe sensor data
  - `v1.0-trainval/` — metadata JSONs
  - `maps/`, `expansion/`, `basemap/` — HD map data
  - `lidarseg/` — semantic segmentation labels
- Key point: scenes are **not** stored as separate folders. nuScenes organizes by sensor (`samples/CAM_FRONT/`, `sweeps/LIDAR_TOP/`, ...) and the `v1.0-trainval/*.json` metadata maps each sample to a scene token. SplatAD selects scenes at train time via `--sequence`.
- Flagged: the `.tgz` tarballs are still on disk (~314 GB reclaimable).

---

## Exchange 2: "what does sweeps mean?"

User followed up with the most productive question of the session. Claude explained the nuScenes samples/sweeps distinction:

- **`samples/`** = keyframes. Annotated at 2 Hz, cross-sensor time-synchronized, hand-labeled with 3D cuboids and attributes. Used for evaluation.
- **`sweeps/`** = everything else. Raw sensor readings between keyframes. Cameras ~12 Hz, LiDAR 20 Hz, radars 13 Hz. Unlabeled.

Why sweeps matter for SplatAD / 3DGS specifically:

1. **View count.** 3DGS quality scales with input-view count. Sweeps give ~5–10× more camera frames and LiDAR scans per scene than keyframes alone.
2. **Ego-motion modeling.** LiDAR sweeps at full rate are needed to correctly compensate for ego-motion during a single scan rotation.
3. **Dynamic actor reconstruction.** Dense temporal sampling lets the model decompose moving objects from static background.

---

## Exchange 3: "what was 10min data loading phase"

Referenced the user's own devlog entry from the same day:
> "Data loading phase took >10 min (trainval metadata + LiDAR ego-motion compensation for all sweeps)"

Claude broke this down into three components:

1. **Loading v1.0-trainval metadata.** The nuScenes devkit loads all 13 JSONs from `v1.0-trainval/` on import and builds in-memory token→record hash tables for all 850 scenes, even if only one is being trained. This alone takes a few minutes.

2. **LiDAR ego-motion compensation for all sweeps.** The expensive part. A spinning LiDAR at 20 Hz takes 50 ms to complete one rotation, during which the ego vehicle moves ~0.7 m at 50 km/h. Each point's timestamp must be mapped back to the ego pose at the moment it was captured, then un-warped into a consistent frame. SplatAD does this for every LiDAR sweep in the training sequence, not just keyframes.

3. **Camera intrinsics/extrinsics assembly.** Minor cost.

One-shot preprocessing — cached for the rest of the run.

---

## Exchange 4: "elaborate on each point's timestamp has to be mapped back..."

The technical core of the session. Claude explained LiDAR motion skew and compensation in depth:

**The problem:**
- A camera is a (near-)instantaneous snapshot; all pixels share a timestamp.
- A spinning LiDAR is **not** a snapshot. At 20 Hz → 50 ms per revolution. During those 50 ms the car moves: 0.7 m at 50 km/h, plus pitch/roll/yaw changes.
- A point at azimuth 0° and a point at azimuth 180° were captured from two different ego poses, 25 ms apart.
- Raw LiDAR points come out as `(x, y, z, intensity, ring_id, timestamp_offset)` in the sensor's **instantaneous** frame at firing time.
- Naively dumping points into one cloud produces **motion skew** — straight walls appear curved or sheared. Sometimes called LiDAR rolling shutter.

**The un-warping procedure (per point):**
1. Recover exact capture time: `t_point = t_scan_start + timestamp_offset`.
2. Look up the ego pose at `t_point` via interpolation from `ego_pose.json` (sampled at ~100 Hz). Linear for translation, SLERP (spherical linear interpolation) for rotation quaternions. Result: `T_world_ego(t_point)`, a 4×4 rigid transform.
3. Transform point to world: `p_world = T_world_ego(t_point) · T_ego_lidar · p_lidar`, where `T_ego_lidar` is the fixed sensor calibration.
4. Transform back into a canonical frame (usually the keyframe timestamp): `p_canonical = T_world_ego(t_ref)⁻¹ · p_world`.

Net effect: every point is shifted to "where it would have been if the car had stayed at `t_ref` the whole time."

**Why SplatAD needs it:**
- Rendering loss compares simulated rays/points against real LiDAR returns. If the real returns are still motion-skewed, the ray origins don't match and the loss is garbage.
- Incidence-angle-dependent ray-drop and intensity MLPs depend on correct ray direction, which depends on correct sensor origin, which depends on correct pose-at-time.

**Cost of the preprocessing:**
- HDL-32E ~60k points per scan × ~400 scans per 20-s scene × 60k = ~24M points for a single scene.
- Pose SLERP per point is not trivially vectorizable.
- Done for all LiDAR sweeps, not just keyframes.
- → few minutes of honest CPU work before training begins.

---

## Exchange 5: CORRECTION — SplatAD uses Design B, not GT un-warping

User pressed back on Claude's framing: "So the real lidar return is compensated lidar recon, and SplatAD evaluate its Gaussian model against it?"

Claude then **corrected** its own previous answer. This is the most important factual content of the session.

There are two possible designs:

- **Design A — "compensate the ground truth":** un-warp real LiDAR into a single reference pose, render from that single pose, compare. Simpler.
- **Design B — "simulate rolling shutter on the render side":** keep the real LiDAR raw and time-indexed. Teach the renderer to cast rays with per-ray time-varying ego poses. Comparison happens against raw sensor data.

**SplatAD uses Design B.** Rolling-shutter-aware rasterization for both camera and LiDAR is a headline contribution of the CVPR 2025 paper.

Implementation (as Claude recalled from the paper, not verified against the raw source):
- SplatAD represents a full LiDAR revolution as a 2D panoramic image (rings × azimuth bins).
- Instead of one pose per revolution, it **tiles** the panorama and assigns each tile its own interpolated ego pose from per-point timestamps.
- Rasterization proceeds per-tile: tile at azimuth 0° uses `T_world_ego(t_start)`, tile at azimuth 90° uses `T_world_ego(t_start + 12.5 ms)`, and so on.
- Rendered panorama carries the same motion skew pattern as the real scan. Comparison against raw GT cancels the skews and produces a clean loss.

> **Correction scope:** the 10-min preprocessing still computes the same per-point/per-tile ego poses via SLERP; but the **consumer** is the renderer's tile-pose table, not an un-warped GT point cloud. The ground truth LiDAR stays in its native per-point-timed form.

**Why Design B is better (two reasons):**
1. **Information preservation.** Motion skew isn't noise — it encodes ego dynamics. A sim that emits un-skewed output is immediately distinguishable from real data and not plug-compatible with detectors trained on skewed data.
2. **Loss fidelity.** Compensating GT injects interpolation error directly into the training target. Design B keeps interpolation error on the rendering side; the raw sensor reading remains immutable reference.

**Tradeoff:** the rasterizer is more complex — can't rasterize from one camera pose, has to handle time-varying poses across the output image. That's the CUDA work owned by SplatAD/neurad-studio.

> **Open question (flagged in session):** the tile-level implementation detail above is Claude's recollection of the paper. Not verified against `raw/` — the SplatAD CVPR 2025 paper has not been ingested into the wiki. Cross-check before citing as fact.

---

## Exchange 6: "what's 'pose' mean in our discussions?"

Claude defined pose compactly:
- **Pose = position + orientation** — 6 degrees of freedom (3 translation + 3 rotation).
- Usually stored as a **4×4 rigid transform matrix** `T = [[R, t], [0, 1]]`.
- `T_world_ego` is the car's pose in world coordinates — changes over time.
- `T_ego_lidar` is the sensor mounting calibration — fixed per scene.
- Composition: `T_world_lidar(t) = T_world_ego(t) · T_ego_lidar`.
- Rendering requires pose. Rolling-shutter discussion was entirely about "what pose do I use for this pixel/point?" having different answers across one output frame.

Cross-linked to existing `gps-imu.md` (already covers ego-pose sensors and their role).

---

## Exchange 7: "what's the annotations for in Nuscenes dataset?"

Claude covered what annotations are and what they're used for.

**What they are:**
- Hand-drawn 3D bounding box labels at every keyframe (2 Hz) by human annotators.
- Stored in `sample_annotation.json`.
- Fields: category (one of 23 classes), 3D cuboid (`translation`, `size`, `rotation` quaternion), `instance_token`, `visibility_token`, `attribute_tokens`, `num_lidar_pts`, `num_radar_pts`.
- Plus `lidarseg/` — per-point LiDAR semantic labels, separate from cuboids.

**Ecosystem uses:**
- 3D object detection benchmark (CenterPoint, BEVFormer, BEVFusion)
- Multi-object tracking (via stable `instance_token`)
- Motion forecasting / trajectory prediction
- LiDAR semantic segmentation
- Occupancy prediction / panoptic segmentation

**Specific uses in our pipeline** (roadmap phases from `PROGRESS.md`):
- Phase 1 (domain gap): detector-based sim-to-real measurement — "train CenterPoint/BEVFormer on sim vs real, compare AP." Needs cuboids for both training the detector and scoring its output.
- Phase 2 (LiDAR realism): same pattern, different modality — "does CenterPoint perform equally on sim vs real LiDAR?"
- Phase 3 (scene editing): **structural** — annotations become an input to 3DGS training, not just eval. SplatAD's dynamic actor decomposition tags Gaussians with `id = instance_token` so per-actor Gaussian sets can be learned, then later moved/removed/transplanted. Cuboid poses are interpolated from 2 Hz to sweep rate during rendering.

---

## Exchange 8: CORRECTION — cuboids are used in BOTH Phase 1 and Phase 2

User pushed back: "it looks that cuboids are always used for detection comparison between sim data and real data, in both phase 1 and phase 2."

Claude agreed and corrected:

**Clean division of what needs annotations vs. what doesn't:**

What does NOT need annotations (in any phase):
- 3DGS reconstruction training (unsupervised on raw sensor data + ego poses)
- Image-level gap metrics: PSNR, SSIM, LPIPS, FID, KID
- LiDAR-level geometric metrics: Chamfer distance, intensity histogram, ray-drop accuracy

What DOES need annotations (in both Phase 1 and Phase 2):
- **Any detector-based evaluation.** Annotations serve two distinct roles:
  1. Training the detector itself (supervised — weights exist because someone used cuboids as target)
  2. Scoring detector output (AP requires matching predicted boxes against GT boxes)

Phase 3 additionally uses annotations as a structural input to reconstruction (dynamic actor decomposition via `instance_token`).

**Honest summary:** cuboids are the common currency of sim-to-real gap measurement throughout Phases 1 and 2. Phase 2 is continuing the same detector-based pattern against a different sensor modality, not introducing a new use.

---

## Exchange 9: "what does token mean?"

Final topic. Claude explained the nuScenes token model:

- A **token** is a 32-char hex string — a primary key for records in nuScenes metadata.
- nuScenes stores metadata as a set of JSON files acting like relational DB tables: `scene.json`, `sample.json`, `sample_data.json`, `sample_annotation.json`, `instance.json`, `ego_pose.json`, `calibrated_sensor.json`, `category.json`, `attribute.json`, `visibility.json`.
- Each row has its own `token` primary key; rows reference other rows by token (foreign keys). Every field ending in `_token` is a cross-reference.
- The nuscenes-devkit loads all JSONs on startup and builds O(1) token→record hash tables. This is part of what the >10-min preprocessing was doing.

**Key tokens for our work:**

- `sample_token` — identifies one keyframe (2 Hz synchronized moment across all sensors).
- `sample_data_token` — identifies one sensor reading file. A sample has multiple sample_data entries (one per sensor). Sweeps are also `sample_data` rows, with `is_key_frame = false`.
- `instance_token` — **most important for Phase 3 work.** An instance is a single physical object observed across time. Same car in 20 consecutive keyframes → 20 sample_annotation rows sharing the same instance_token. Stable identity across time = free object tracks. SplatAD's dynamic actor Gaussians use `id = instance_token`.
- `ego_pose_token` — identifies one `(timestamp, position, orientation)` row in `ego_pose.json`.
- `calibrated_sensor_token` — identifies a sensor's mounting calibration.
- `scene_token` — 20-second sequence. `scene-0796` is human-readable; underlying token is a hex string.

**Why it matters practically:**
1. **Tracks are free** — `WHERE instance_token = X` gives you a track without running a tracker. This is what makes nuScenes suitable for Phase 3 scene editing with minimal preprocessing.
2. **Everything joins cleanly** — "all LiDAR points inside the cuboid of instance X at time t" is a chain of token lookups.

---

## Notes on reliability

- All technical claims about nuScenes structure are consistent with the nuscenes-devkit source and with `[[caesar2019-nuscenes]]`, already in the wiki. Safe.
- Claims about **SplatAD's rolling-shutter-aware rasterization** are Claude's recollection from the CVPR 2025 paper. The paper is NOT yet ingested into `raw/`. Cross-check before citing the tile-level detail as fact. The high-level claim "SplatAD models LiDAR rolling shutter" is well-established.
- Claims about ego-motion compensation as a general AV preprocessing technique are standard and widely documented.
- No specific numbers were invented. The "~24M points per scene" estimate is ballpark arithmetic, not a measurement.
- The Phase 1/2/3 framing comes directly from the user's `/workspace/PROGRESS.md`, not Claude's invention.
