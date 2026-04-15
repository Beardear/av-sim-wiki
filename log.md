# Log

## [2026-04-12] lint fix | Create LiDAR-GS entity stub to resolve broken link
Fix for the broken link surfaced by the 2026-04-12 lint run (`[[lidar-gs|LiDAR-GS]]` in `claude-nuscenes-lidar-discussion-2026-04-11.md:73`).
- **Created `pages/entities/lidar-gs.md`** — stub entity page authored from the arXiv abstract at https://arxiv.org/abs/2410.05111 (fetched via WebFetch, not saved to `raw/`). Follows the `centerpoint` / `bevformer` / `pointpillars` stub precedent: `sources: []` with a Provenance section citing the canonical paper for future full `/wiki update`. Citation: Chen, Yang, Du, Tang, Xie, Chen, Huo. "LiDAR-GS: Real-time LiDAR Re-Simulation using Gaussian Splatting." arXiv:2410.05111 (v1 Oct 2024, v3 Oct 2025).
- **Content**: frames LiDAR-GS as the most direct comparator to [[splatad]]'s LiDAR path. Captures the four methodological contributions from the abstract (differentiable laser-beam splatting on range-view representation, micro-cross-section beam modeling to eliminate artifacts, neural Gaussian representation with view-dependent cues, dynamic instance decomposition). Claims about specific benchmark numbers, rolling-shutter handling, and dataset choices are **deliberately absent** — flagged as open questions to be resolved when the full paper is ingested.
- **Wired inbound links** from four existing pages to prevent orphan status and to make the new page discoverable from natural reading paths:
  - `pages/concepts/3d-gaussian-splatting.md:30` — wrapped the plain-text mention as `[[lidar-gs|LiDAR-GS]]`.
  - `pages/concepts/lidar-simulation.md:34` — wrapped the main Gaussian-Splatting subsection mention and extended the one-liner with the range-view + multi-channel detail from the abstract.
  - `pages/concepts/lidar-simulation.md:50` — wrapped the "Beam divergence is ignored by most methods except LiDAR-GS" open-problem mention.
  - `pages/entities/splatad.md:62` — wrapped the LiDAR fidelity open-question mention and added a second `[[lidar-gs]]` pointer framing it as the natural Phase 2 head-to-head target.
- **Updated `index.md`**: added `lidar-gs` under **Entities — Systems (3DGS-based)** (between `ho-gaussian` and `gs-roadpatching` to preserve the rough "specialized systems" ordering). Bumped stats to **8 concepts, 26 entities, 7 sources, 0 comparisons**.
- **Broken link resolved**: `[[lidar-gs|LiDAR-GS]]` in `claude-nuscenes-lidar-discussion-2026-04-11.md:73` now resolves to the new file.
- **Not auto-fixed**: the `log.md:114` `[[slug|Display Name]]` false-positive (literal prose in a past lint-fix entry) is unchanged.
- **Next step**: when Phase 2 head-to-head work begins, run `/wiki update https://arxiv.org/pdf/2410.05111` to replace the stub with a properly sourced page. The current stub's open questions — quantitative Chamfer/intensity/ray-drop gaps vs [[splatad]], rolling-shutter handling, dataset coverage — are all unanswerable from the abstract alone.

## [2026-04-12] ingest | Claude session — nuScenes data model empirical verification
Source: `raw/2026-04-12-nuscenes-data-model-verification.md` — internal Claude Code session (Opus 4.6 1M, v2.1.98). Ingested via `/wiki update current session`. Provenance source, not an external citation. Extends the 2026-04-11 `claude-nuscenes-lidar-discussion` session: that one introduced the Data Model section of `pages/entities/nuscenes.md` conceptually; this one verified every quantitative claim empirically by loading the on-disk trainval JSONs and counting with `len()`, then produced a phase→data mapping for `/workspace/PROGRESS.md`.

- **Created source page**: `pages/sources/claude-nuscenes-data-model-discussion-2026-04-12.md` — follows the `claude-nuscenes-lidar-discussion-2026-04-11` and `claude-gps-imu-discussion-2026-04-09` provenance-source precedents. Notes that the row counts and directory invariants are empirically verified, but that the per-scene `calibrated_sensor` claim still needs confirmation against the nuscenes-devkit documentation.
- **Updated `pages/entities/nuscenes.md`** substantially:
  - Added a new **"v1.0-trainval — Empirically Verified Counts (2026-04-12)"** section with a row-count table for the main metadata tables, a structural-invariants list (`ego_pose` 1:1 with `sample_data` at 2,631,083; `calibrated_sensor` 10,200 = 850 × 12 per-scene; every keyframe has exactly 12 `sample_data` rows; 34,140 of 34,149 keyframes annotated), a per-channel `sample_data` breakdown, and a note that `v1.0-mini` contains scene-0796 — making mini a viable fast-iteration learning substrate.
  - Added a new **"Phase → Data Mapping"** table explicitly marking every table and blob directory as Essential / Useful / Conditional / Not used for Phase 1 / Phase 2 / Phase 3 of the PROGRESS.md roadmap.
  - Rewrote the Samples vs Sweeps subsection to clarify that **`samples/` is a blob directory, not an annotation-bearing structure** — annotations live separately in `sample_annotation.json` and are linked by `sample_token`. Corrects a conflation present in both this session's first pass and, implicitly, in the 2026-04-11 draft.
  - Adjusted `Scale` from ambiguous "1000 scenes" to "1000 total = 850 trainval + 150 test".
  - Added the new source to frontmatter, bumped `updated:` to 2026-04-12, and added a Related entry.
- **Updated `index.md`**: added the new source under Sources, bumped stats to **8 concepts, 25 entities, 7 sources, 0 comparisons**. Index `updated:` bumped to 2026-04-12.
- **No new concept/entity pages** — the material extended an existing primary entity page rather than creating new cross-cutting concepts. The pedagogy output (90-min data-literacy learning plan) is explicitly out of scope for the wiki and lives in `/workspace/PROGRESS.md` instead.
- **Key takeaways / corrections against prior framings**:
  - `calibrated_sensor` is per-(scene × sensor), **not** per-log. The 10,200 = 850 × 12 identity is arithmetically unambiguous.
  - `sample_data` has **2,631,083** rows on trainval, not the "~1.4M" figure some sources cite — the 1.4M is camera-only. The other ~1.2M are LiDAR + radar + sweeps.
  - `samples/` vs `sweeps/` ↔ `is_key_frame` is an **exact bijection** (409,788 keyframe rows all under `samples/`, 2,221,295 sweep rows all under `sweeps/`, zero cross-contamination). The directory name encodes the flag losslessly.
  - `instance.json` has **64,386 unique tracked actors** in trainval — this is the Phase 3 actor-editing budget, previously not on any wiki page.
  - `category.json` has **32 entries but only 23 are annotation classes**; the other 9 are lidarseg-only surface labels. Resolves a latent tension with the paper's "23 classes" figure.
  - **Phase 2 adds nothing on disk; Phase 3 adds nothing beyond Phase 1 + annotations.** The empirical phase-mapping confirms that Phase 2 improvements are entirely in-model (ray-drop, intensity), not in data coverage.
- **Open questions flagged**:
  - (1) Is `calibrated_sensor` *strictly* one row per `(scene, sensor)`, or can the schema issue more when calibration changes mid-sequence? Verify against nuscenes-devkit before Phase 3 code relies on uniqueness.
  - (2) What causes the 9 annotation-less keyframes in trainval? Worth logging their sample tokens for Phase 3 filter tests.
  - (3) Carried forward: does SplatAD's rolling-shutter-aware rasterizer consume `ego_pose.json` at the 1:1 `sample_data` granularity or interpolate further? Still blocked on ingesting the SplatAD CVPR 2025 paper — noted in both this and the 2026-04-11 session pages.

## [2026-04-11] ingest | Claude session — nuScenes data model & LiDAR rolling shutter
Source: `raw/2026-04-11-claude-nuscenes-lidar-discussion.md` — internal Claude Code session (Opus 4.6 1M, v2.1.98). Ingested via `/wiki update based on current session` on 2026-04-11. Provenance source, not an external citation. The session originated from the user recording that nuScenes v1.0-trainval blob extraction had finished and then asking a Q&A chain about samples/sweeps, the >10 min preprocessing phase, LiDAR rolling shutter, pose, annotations, and tokens.

- **Created source page**: `pages/sources/claude-nuscenes-lidar-discussion-2026-04-11.md` — follows the `claude-gps-imu-discussion-2026-04-09` precedent as a provenance record. Documents two in-session corrections (SplatAD uses Design B rolling-shutter rendering, not GT un-warping; annotations are used symmetrically in Phase 1 and Phase 2 detector-based evals, not just Phase 2). Flags the SplatAD tile-level implementation detail as Claude recollection, not verified from the raw paper.
- **Created concept page**: `pages/concepts/lidar-motion-compensation.md` — motion skew / rolling shutter in spinning LiDAR, per-point SLERP-interpolated ego-pose correction, the Design A (compensate GT) vs Design B (rolling-shutter-aware rendering) tradeoff, and the empirical >10 min preprocessing cost on nuScenes v1.0-trainval. Cross-links to `splatad`, `lidar-simulation`, `gps-imu`, `nuscenes`, `sensor-coordinate-frames`, `sim-to-real-transfer`.
- **Updated entity pages**:
  - `pages/entities/nuscenes.md` — substantial new "Data Model — Samples, Sweeps, and Tokens" section covering: samples vs sweeps distinction with view-count rationale for SplatAD; token-keyed relational schema with a table of the 10 main metadata files; `instance_token` as the free-tracks hook for Phase 3 dynamic actor work; annotations across phases with the Phase 1/2 symmetry correction. Added source to frontmatter and Related. Cross-linked to `lidar-motion-compensation`.
  - `pages/entities/splatad.md` — added "Rolling-Shutter Modeling" subsection framing it as a CVPR 2025 paper contribution and articulating the Design-B choice; added "Preprocessing Cost (empirical)" subsection grounded in the user's devlog observation. Extended the Dynamic Actors bullet with the `id = instance_token` mapping for nuScenes. Added source, updated Related. (Frontmatter `updated:` normalized to today 2026-04-11; pre-existing value was 2026-04-12 which post-dates the project clock.)
- **Updated concept pages**:
  - `pages/concepts/lidar-simulation.md` — added motion skew / rolling shutter as a seventh fidelity dimension alongside the existing six. Cross-link to new concept page. Added source.
- **Updated `index.md`**: added the new concept page under Concepts, added the new source under Sources, bumped stats to **8 concepts, 25 entities, 6 sources, 0 comparisons**. Also normalized the index `updated:` field from 2026-04-12 to 2026-04-11 to match today's project clock.
- **Key takeaways**:
  - The ~10-minute "data loading phase" observed when launching `ns-train splatad ... nuscenes-data --sequence 0796` is dominated by ego-pose SLERP interpolation for every LiDAR sweep in the training sequence, plus the nuscenes-devkit building in-memory hash tables over the full v1.0-trainval metadata even for single-scene training.
  - [[splatad]]'s design is to **simulate** rolling shutter rather than un-warp the GT. This matters for sim-to-real: motion skew encodes ego dynamics and must survive into sim output to remain plug-compatible with downstream detectors.
  - Annotations are used symmetrically in Phase 1 and Phase 2 whenever a detector-based sim-to-real metric is computed — both to have trained the detector in the first place and to score its output. Only pixel/geometric metrics (PSNR/SSIM/LPIPS/FID, Chamfer/intensity/ray-drop) skip annotations entirely.
  - `instance_token` is the stable primary key that makes dynamic actor decomposition in Phase 3 cheap — SplatAD's Gaussian `id` tag is bound directly to it.
- **Open questions flagged**:
  - The exact rolling-shutter implementation in SplatAD (per-tile on panoramic, per-ray in kernel, other?) — unverified; motivates ingesting the SplatAD CVPR 2025 paper into `raw/` as the obvious next source.
  - Does the SplatAD paper include a quantitative ablation on rolling-shutter handling, or is its importance only argued theoretically?
  - Do other 3DGS-for-LiDAR methods (LiDAR-GS, GS-LiDAR) handle rolling shutter, or is it a SplatAD-specific strength?
  - Does the SplatAD dataparser cache the interpolated pose tables across runs or recompute every launch? Relevant to iteration cost.

## [2026-04-12] ingest | gsplat GitHub README (Nerfstudio)
Source: `raw/gsplat-github-readme.md` — README for `github.com/nerfstudio-project/gsplat` (fetched via curl from `raw.githubusercontent.com/.../main/README.md`). Citation: Ye, Li, Kerr, Turkulainen, Yi, Pan, Seiskari, Ye, Hu, Tancik, Kanazawa. "gsplat: An open-source library for Gaussian splatting." JMLR 26(34), 1–17, 2025. White paper at arXiv:2409.06765 (not yet ingested).

- **Created source page**: `pages/sources/gsplat-github-readme.md` — citation, key contributions (4× memory / 15% time vs. official 3DGS on mip-NeRF 360; arbitrary batching; 3DGUT and PPISP integrations), install paths, institutional contributors, limitations (README-only, white paper not ingested).
- **Created entity page**: `pages/entities/gsplat.md` — frames gsplat as a library/backbone, not a full reconstruction system. Documents the Nerfstudio lineage and the SplatAD fork relationship.
- **New index subcategory**: **Entities — Libraries & Infrastructure**, placed between 3DGS Systems and NeRF Systems.
- **Updated existing pages**:
  - `pages/concepts/3d-gaussian-splatting.md` — added a "Canonical Implementation" section citing gsplat's perf numbers and the SplatAD fork relationship. Added `gsplat-github-readme` to sources, bumped `updated:` to 2026-04-12.
  - `pages/entities/splatad.md` — expanded the CUDA rasterization bullet with a direct repo citation: `gsplat@git+https://github.com/carlinds/splatad.git` pinned at `neurad-studio/pyproject.toml:68`, and `from gsplat.rendering import lidar_rasterization, rasterization` at `neurad-studio/nerfstudio/models/splatad.py:61`. Added `[[gsplat]]` to Related. Added source, bumped `updated:`.
  - `pages/entities/neurad.md` — noted that the 3DGS path in neurad-studio depends on the SplatAD gsplat fork while the NeRF path is independent. Added `[[gsplat]]` to Related. Added source, bumped `updated:`.
- **Updated `index.md`**: added gsplat entity under the new subcategory, added source page, bumped stats to **7 concepts, 25 entities, 5 sources, 0 comparisons**.
- **Key takeaways**:
  - SplatAD is not "built from scratch" — it is the upstream gsplat camera rasterization kernel plus a SplatAD-authored `lidar_rasterization` kernel plus learned CNN/MLP decoders. Worth knowing when reading `splatad.py` or debugging kernel-level issues.
  - gsplat is a moving target: NVIDIA 3DGUT (Apr 2025), arbitrary batching (May 2025), and PPISP (Jan 2026) are all post-SplatAD-CVPR-2025 additions that the fork may or may not have absorbed.
  - Upstream gsplat is AV-agnostic: no unbounded-scene contraction, no LiDAR, no dynamic actors. Everything AV-specific lives downstream in SplatAD/neurad-studio.
- **Open questions flagged**: (1) how divergent is the SplatAD fork from current upstream, is a rebase worth it; (2) does 3DGUT's ray-traced Unscented Transform help thin-structure/far-field artifacts for SplatAD's Phase 1 failure-mode analysis; (3) the JMLR/arXiv white paper is the right next source to ingest if we need kernel-level or benchmark-level detail.

## [2026-04-11] ingest | Caesar et al. 2019 — nuScenes dataset paper
Source: `raw/caesar2019-nuscenes.pdf` — Caesar, Bankiti, Lang, Vora et al. "nuScenes: A Multimodal Dataset for Autonomous Driving." CVPR 2020 (arXiv:1903.11027). Supplemented with nuScenes devkit source code (GitHub) and nuscenes.org website (JS-rendered, limited direct access).

- **Created source page**: `pages/sources/caesar2019-nuscenes.md` — covers sensor suite (all 12 channels by name), specifications (Table 2), car setup, coordinate frames, synchronization, NDS/sAMOTA metrics, PointPillars baselines.
- **Created concept page**: `pages/concepts/sensor-coordinate-frames.md` — explains why cameras use CV convention (Y-down) while LiDAR/radar use robotics convention (Z-up), how nuScenes bridges them via `calibrated_sensor` extrinsics, and implications for simulation rendering.
- **Updated entity pages**:
  - `pages/entities/nuscenes.md` — substantially expanded sensor suite section with per-channel table, specs, coordinate frame reference, synchronization details, MCL localization, NDS metrics section, new open question on localization impact. Added `caesar2019-nuscenes` to sources.
  - `pages/entities/pointpillars.md` — added nuScenes baseline results (single vs multi-sweep, test set numbers, pre-training findings). Added `caesar2019-nuscenes` to sources.
- **Updated concept pages**:
  - `pages/concepts/gps-imu.md` — replaced vague "fused ego-pose" for nuScenes with concrete MCL details (≤10 cm, LiDAR + odometry + HD map). Added `caesar2019-nuscenes` to sources.
  - `pages/concepts/lidar-simulation.md` — added nuScenes LiDAR specs as concrete example in point density dimension. Added `caesar2019-nuscenes` to sources.
  - `pages/concepts/sim-to-real-transfer.md` — added `caesar2019-nuscenes` to sources (NDS metrics originate here).
- **Updated `index.md`**: added source page and concept page, bumped stats to 7 concepts, 24 entities, 4 sources, 0 comparisons.
- **Key takeaways**:
  - nuScenes LiDAR is 32-beam, ≤70 m range, 20 Hz — notably lower beam count and range than Waymo's 64-beam sensor. Simulation must target these exact specs.
  - Sensor synchronization (LiDAR-triggered camera exposure) is elegant but means not every LiDAR scan has a corresponding camera frame — relevant for multi-modal sim rendering.
  - Center-distance matching (not IOU) is the correct metric for nuScenes detection — matters when evaluating sim-to-real on small objects.
  - Radar remains underexplored in the paper and the field. No competitive radar-only methods existed at time of publication.
- **Open questions flagged**: localization accuracy impact on reconstruction quality (nuScenes ≤10 cm MCL vs Waymo); radar fusion for detection.

## [2026-04-10] ingest | NVIDIA Cosmos, Omniverse, DRIVE Sim
Source: `raw/nvidia-cosmos-web-research-2026-04-10.md` — compiled from NVIDIA Newsroom (CES Jan 2025, GTC Mar 2025), arXiv:2501.03575, NVIDIA developer pages, and RidgeRun ecosystem explainer.
- Created source page: `pages/sources/nvidia-cosmos-web-research-2026-04-10.md`
- Created 3 new entity pages: `pages/entities/nvidia-cosmos.md`, `pages/entities/nvidia-omniverse.md`, `pages/entities/nvidia-drive-sim.md`
- New index subcategory: **Entities — Platforms (NVIDIA)** added above Perception Models.
- Updated existing pages: `sim-to-real-transfer.md` and `lidar-simulation.md` (added cross-references to new NVIDIA entities); `overview.md` (added Industrial Platforms section under The Landscape).
- Key takeaways:
  - Cosmos (generative WFMs) and DRIVE Sim (industrial AV sim) are complementary to the per-scene neural reconstruction systems tracked in this wiki.
  - Cosmos Transfer (structured input to photoreal video) is the most AV-relevant Cosmos model.
  - NuRec (Omniverse neural reconstruction) is conceptually analogous to SplatAD/NeuRAD but details are proprietary.
  - AlpaSim (open-source closed-loop AV testing) is the closest NVIDIA counterpart to UniSim's closed-loop capability.
- Open questions flagged: NuRec reconstruction quality vs SplatAD/NeuRAD; Cosmos Transfer as inpainting for 3DGS gaps.
- Stats: now **6 concepts, 24 entities, 3 sources, 0 comparisons**.

## [2026-04-10] add | Perception model entity pages (CenterPoint, BEVFormer, PointPillars)
Created three entity pages from general knowledge to fill the stale-content gap flagged in the 2026-04-09 lint report. All three were referenced as load-bearing baselines across multiple wiki pages but had no dedicated entries.
- New pages: `pages/entities/centerpoint.md`, `pages/entities/bevformer.md`, `pages/entities/pointpillars.md`. All marked `sources: []` with a Provenance section pointing at the canonical paper for future `/wiki update`.
- New index subcategory: **Entities — Perception Models** added below Datasets.
- Wired inbound links from existing mentions: `nuscenes.md:32` (CenterPoint, BEVFormer), `lidar-simulation.md:13` (CenterPoint, PointPillars), `sim-to-real-transfer.md:23` and `:45` (CenterPoint, BEVFormer). Each existing page's `updated:` bumped to 2026-04-10.
- Stats: now **6 concepts, 21 entities, 2 sources, 0 comparisons**.
- CenterPoint is positioned as the project's **primary AP-delta evaluator** for the Phase 1 sim-to-real measurement (cross-references `sim-to-real-transfer.md:45`).
- BEVFormer is positioned as the camera-side counterpart to CenterPoint's LiDAR side.
- PointPillars is framed as a faster, simpler LiDAR baseline — not the primary evaluator.

## [2026-04-09] lint fix | Date drift and unlinked references in zhou2026
Ran `/wiki lint fix`. Full lint report: 0 orphans, 0 broken links, 0 contradictions (spot-checked), 26/26 pages with valid frontmatter, index consistent.
Auto-fixes applied:
- **Frontmatter date drift**: bumped `updated:` from `2026-04-07` → `2026-04-09` on `pages/entities/kitti.md` and `pages/entities/nuscenes.md` (both had GPS/IMU wiki links added today without the date being bumped).
- **Unlinked references in `pages/sources/zhou2026-av-scenario-review.md`**: wrapped 11 plain-text mentions of existing entity pages with `[[slug|Display Name]]` wiki links — `driving-gaussian`, `tclc-gs`, `ho-gaussian`, `neural-scene-graph`, `neurad` (second "NeuRAD" mention in editing section + Relevance-section "builds on NeuRAD"), `citygaussian`, `suds`, `emernerf`, `unisim`, plus the Table 4 rows for Neural Scene Graph, EmerNeRF, UniSim, Driving Gaussian, TCLC-GS, HO-Gaussian, SUDS, and the Relevance-section "TCLC-GS and HO-Gaussian" prose mention. Also bumped `zhou2026-av-scenario-review.md` `updated:` to `2026-04-09`.
Not auto-fixed (require user decision, writing new prose, or research):
- 6 stale-content missing pages named in `zhou2026-av-scenario-review.md`: Periodic Vibration Gaussian, MARS, DNMP, Panoptic Neural Fields, S-NeRF, HGS-Mapping.
- 3 missing-page suggestions: closed-loop simulation (concept), actor decomposition/tracking (concept), ego-pose noise sensitivity of reconstruction (open-question comparison).

## [2026-04-09] ingest | Claude GPS/IMU discussion (provenance source)
Source: `raw/2026-04-09-224828-search-my-wiki-whats-imu.txt` — exported transcript of the Claude Code session that authored `gps-imu.md` earlier today. Ingested via `/wiki update`.
- Created source page: `pages/sources/claude-gps-imu-discussion-2026-04-09.md`. Explicitly framed as **provenance**, not external citation — documents how `gps-imu.md` was authored and records the reasoning, but cannot validate the page's claims.
- Updated `pages/concepts/gps-imu.md`: appended the source slug to `sources:` (was `[]`) and added a provenance bullet to the Related section with a "not an external citation" caveat.
- Updated `index.md`: added under Sources; stats now **6 concepts, 18 entities, 2 sources, 0 comparisons**.
- Limitations logged on the source page: not peer-reviewed, no novel empirical claims, self-referential. The two open questions on `gps-imu.md` (pose-noise sensitivity of SplatAD/NeuRAD; synthetic IMU for VIO) are carried forward unresolved.

## [2026-04-09] add | GPS/IMU concept page
Created `pages/concepts/gps-imu.md` covering GPS and IMU as fused ego-pose sensors (GNSS/INS). Written from general knowledge — `sources: []`, no raw source ingested.
- Combined GPS and IMU into a single concept page since they are almost always fused in AV stacks.
- Framed as "not a simulation target but a load-bearing input" — pose quality from GPS/IMU propagates into neural reconstruction quality.
- Added inbound links from `kitti.md` (converted existing "GPS/IMU" text to wiki link) and `nuscenes.md` (added sensor-suite bullet).
- Updated `index.md`: now 6 concepts, 18 entities, 1 source, 0 comparisons.
- Two open questions flagged: pose-noise sensitivity of SplatAD/NeuRAD, and whether any method emits synthetic IMU for VIO.

## [2026-04-07] init | Wiki bootstrapped
Created wiki structure for Autonomous Driving Simulation domain. Seeded with initial pages derived from project knowledge (PROGRESS.md) covering: SplatAD, NeuRAD, 3D Gaussian Splatting, nuScenes, KITTI, LiDAR simulation, scene editing, sim-to-real transfer, and key reference papers.

## [2026-04-07] ingest | Zhou et al. 2026 — AV Scenario Generation Review
Source: `raw/AV_review.pdf` (15 pages, image-based PDF). Zhou, Chu, Tian et al., Chinese Journal of Mechanical Engineering 39, 2026.
- Created source page: `pages/sources/zhou2026-av-scenario-review.md`
- Created 5 new entity pages: CityGaussian, TCLC-GS, HO-Gaussian, UniSim, EmerNeRF
- Created 1 new concept page: AV Sim Framework (4-Step Pipeline)
- Updated 3 existing pages: scene-editing.md (5 editing patterns), neurad.md (closed-loop recognition), street-gaussians.md (4D SH details)
- Updated index.md (now 5 concepts, 12 entities, 1 source)
- Key takeaway: Survey confirms our architecture direction; LiDAR simulation is barely covered (niche opportunity); proposed 4-step framework (reconstruct→edit→sim→deploy) is conceptual but not yet implemented end-to-end.

## [2026-04-07] lint | Wiki health check and fixes
Lint findings:
- 2 orphan pages fixed (av-sim-framework, emernerf — added inbound links from overview.md, scene-editing.md)
- 7 stale `sources:` fields updated to reference zhou2026-av-scenario-review
- 4 pages correctly left with `sources: []` (no formal source ingested yet)
- 0 broken links, 0 contradictions
- 6 missing entity pages created: Driving Gaussian, Neural Scene Graph, SUDS, Gaussian Editor, Gaussian Grouping, Waymo Open Dataset
- Wiki now at 5 concepts, 18 entities, 1 source, 0 comparisons

## [2026-04-07] lint #2 | Follow-up health check
- 2 orphans fixed: suds (linked from overview.md, citygaussian.md), waymo-open-dataset (linked from overview.md, nuscenes.md)
- Added additional cross-references for driving-gaussian, gaussian-editor, gaussian-grouping, neural-scene-graph
- 0 orphans, 0 broken links
