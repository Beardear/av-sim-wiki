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
