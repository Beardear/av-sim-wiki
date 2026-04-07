# Log

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

## [2026-04-07] ingest | 5 Gemini Q&A Clippings (AV-related content)
Ingested 5 Gemini conversation clippings from `Clippings/` folder. Filtered out non-AV topics (security exploits, Azure DevOps, nvm.sh, Claude Code workflows).

**New source pages (5):**
- `gemini-nerf-gs-fundamentals.md` — NeRF rendering equation, sampling, density/sigma, SfM, LiDAR in NeRF/GS, SLAM comparison, KITTI sensor details
- `gemini-gs-speed-voxel-ray.md` — Why GS is faster (rasterization vs ray marching, SH vs MLP, no empty-space compute), voxel vs ray-driven paradigms
- `gemini-comma-ai-av-comparison.md` — comma.ai/openpilot strategy, L2 (openpilot/Tesla FSD) vs L4 (Waymo/Zoox) comparison
- `gemini-radar-doppler-dynamic-gs.md` — Doppler limitation in static GS, compositional scene decomposition, RCS, BEV representation
- `gemini-depth-maps-datasets.md` — GS floaters/phantom braking, dataset comparison (KITTI/nuScenes/Waymo/Argoverse), open-source frameworks

**New concept pages (6):**
- `nerf.md` — NeRF fundamentals (volume rendering equation, hierarchical sampling, density as attenuation coefficient)
- `structure-from-motion.md` — SfM pipeline, SfM vs SLAM, role in NeRF/GS preprocessing
- `stereo-vision.md` — Stereo depth estimation, rectification, KITTI's dual camera rationale
- `radar-simulation.md` — RCS, Doppler, radar in 3DGS, Doppler limitation of static scenes
- `bird-eye-view.md` — BEV as standard AV perception representation, BEV-based architectures
- `sensor-fusion.md` — Multi-modal fusion strategies, floater problem, calibration chain

**New entity pages (7):**
- `colmap.md` — Standard SfM tool for NeRF/GS preprocessing
- `comma-ai.md` — comma.ai/openpilot open-source ADAS
- `carla.md` — Unreal Engine closed-loop simulator
- `argoverse.md` — Argoverse 2 dataset (310m LiDAR, HD maps)
- `mmdetection3d.md` — OpenMMLab 3D detection/fusion framework
- `autoware.md` — Full-stack open-source AD framework (ROS2)
- `open3d.md` — 3D geometry processing library

**Updated existing pages (5):**
- `3d-gaussian-splatting.md` — Added rendering pipeline detail, adaptive density control, floater problem, expanded NeRF comparison table
- `lidar-simulation.md` — Added LiDAR as physical reality check, depth map cross-validation, radar link
- `kitti.md` — Added sensor details (monochrome vs color rationale, ground truth methodology, calibration chain, benchmark tasks)
- `nuscenes.md` — Added dataset comparison table, new entity links
- `scene-editing.md` — Added dynamic object simulation section (compositional decomposition, deformation MLPs, skeleton-driven splatting, asset libraries)

**Final stats:** 11 concepts, 25 entities, 6 sources, 0 comparisons

## [2026-04-07] lint #3 | Post-ingest health check
Lint findings:
- 0 broken links (all 39 unique link targets resolve)
- 0 stale source references (all 6 source slugs in frontmatter exist)
- 3 orphan source pages fixed: added inbound [[wiki-links]] from relevant pages
  - `gemini-comma-ai-av-comparison` ← linked from comma-ai.md
  - `gemini-gs-speed-voxel-ray` ← linked from nerf.md
  - `gemini-depth-maps-datasets` ← linked from argoverse.md
- 0 missing entity pages
- Result: 0 orphans, 0 broken links, 0 stale sources
