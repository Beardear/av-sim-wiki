---
title: "Zhou et al. 2026 — AV Scenario Generation Review (NeRF & 3DGS)"
type: source
created: 2026-04-07
updated: 2026-04-09
tags: [review, nerf, 3dgs, scene-reconstruction, scene-editing, survey]
sources: [zhou2026-av-scenario-review]
---

# Autonomous Driving Scenario Generation Based on NeRF or 3DGS: State-of-the-Art Investigations, Reviews, and Perspectives

**Authors:** Wei Zhou, Wenbo Chu, Yue Tian, Tianyang Fei, Yulong Ding, Xiaolin Tang, Keqiang Li
**Venue:** Chinese Journal of Mechanical Engineering, Vol. 39, 2026 (100093)
**DOI:** 10.1016/j.cjme.2025.100093
**Received:** July 2024 | **Revised:** June 2025 | **Accepted:** September 2025

## Key Contributions

1. **Comprehensive survey** of NeRF and 3DGS applied to autonomous driving scene reconstruction, covering ~124 references across major databases (Web of Science, IEEE Xplore, arXiv).
2. **Three-category taxonomy** of the field (Fig. 5): model improvement, editing improvement, and challenging reconstruction — each subdivided by NeRF vs. 3DGS baseline.
3. **Development timeline** (Fig. 1): Traces the evolution from original NeRF (2020) through 3DGS (Aug 2023) to AV-specific dynamic systems (2024), with a parallel track for each.
4. **Proposed 4-step framework** for a "Real Driving Scenario SIM" platform (Section 4): reconstruct → edit → import to sim (CARLA/SUMO) → deploy perception & decision-making algorithms.
5. **Tabulated summaries** (Tables 1–4) cataloging representative works with their baselines, key features, and strengths.

## Technical Details

### Taxonomy (Section 3)

**3.1 Model Improvement** — Improving reconstruction quality:
- *Quality enhancement*: NeRF++ (unbounded scenes), Mip-NeRF (anti-aliasing), Zip-NeRF (hash grids + anti-aliasing), BARF (joint pose estimation), GeoGaussian (geometry-aware), Scaffold-GS (structured anchors), GES (generalized exponentials), MipSplatting (high-frequency artifacts)
- *Dynamics & deformation*: D-NeRF, k-NeRF, Dynamic 3D Gaussians, Deformable 3D Gaussians, 4D-GS, GauFRe, 4D Gaussian Splatting, GaussianFlow
- *AV-specific*: [[neurad]] (first closed-loop evaluation), [[driving-gaussian|Driving Gaussian]] (incremental static + composite dynamic), [[street-gaussians]] (4D SH appearance model), Periodic Vibration Gaussian (single formula for static+dynamic), [[tclc-gs|TCLC-GS]] (hybrid explicit LiDAR + implicit NeRF), [[ho-gaussian|HO-Gaussian]] (combined LiDAR + camera point density)

**3.2 Editing Improvement** — Modifying reconstructed scenes:
- *NeRF-based*: Editing NeRF (shape/appearance), CLIP-NeRF (text-driven), Instruct-NeRF2NeRF (instruction-based style transfer)
- *3DGS-based*: Gaussian Editor (text-guided fine-grained editing), Gaussian Grouping (semantic reconstruction + editing), GaussianFrosting (mesh parameterization of Gaussians), TIP-Editor (text+image prompts), GaussCtrl (text-driven multi-view consistent editing)
- *AV-specific editing*: [[neural-scene-graph|Neural Scene Graph]] (actor decomposition), MARS (modular), [[neurad|NeuRAD]] (dynamic scenario with trajectory changes), [[street-gaussians]] (rotate, move actors)

**3.3 Challenging Reconstruction** — Hard scenarios:
- *Large-scale*: Mega-NeRF (parallel sub-models), Block-NeRF (city-scale blocks), Switch-NeRF (learned decomposition), [[citygaussian|CityGaussian]] (LoD + divide-and-conquer)
- *Low-light*: HDR-NeRF (wide lighting range), NeRF in the Dark (noisy low-light)
- *Complex dynamic*: [[suds|SUDS]] (static + dynamic + far-field decomposition from 1700 videos, km-scale), HGS-Mapping (hybrid 3DGS with 3 Gaussian types), Kerbl et al. (hierarchical 3DGS for large-scale)

### Proposed Framework (Section 4)

Four steps to build a complete AV test system:

**Step 1: Large-scale dynamic scene reconstruction**
- NeRF pipeline (Fig. 6): multi-source sensor input → sky mask + multi-camera + LiDAR prior → static background node + dynamic traffic participants node → volume rendering → color + density output
- 3DGS pipeline (Fig. 7): 2D RGB + multi-camera + LiDAR + sky models → static background + dynamic participants → splatting → RGB images from multiple viewpoints

**Step 2: Free editing of scenarios**
- NeRF editing (Fig. 8): reconstructed scene → editing strategies (rotate, dance, move, translate, oversample, switch location) → re-encoding (positional, shape code, lighting component) → edited scene
- 3DGS editing (Fig. 9): 5 editing patterns:
  - **Option A**: Oversample + rotate dynamic actors in composite scene
  - **Option B**: Move dynamic actors to new positions
  - **Option C**: Delete dynamic actors from composite scene (static background remains)
  - **Option D**: Delete dynamic actors (both static+dynamic reconstructed)
  - **Option E**: Move static actors in reconstructed scene

**Step 3: Import to simulation software**
- Edited scenarios imported into CARLA or SUMO for perception and decision-making algorithm testing.

**Step 4: Algorithm deployment**
- Self-contained AV driving test system based on reconstructed scenarios. Includes scene reconstruction, autonomous editing, and decision-making deployment.

### Key Systems Referenced (Table 4 — AV-specific)
| System | Baseline | Focus |
|--------|----------|-------|
| [[neural-scene-graph|Neural Scene Graph]] | NeRF | Model improvement + editing (actor decomposition) |
| DNMP | NeRF | Deformable mesh primitives |
| Panoptic Neural Fields | NeRF | Improved scene graph with semantic segmentation |
| S-NeRF | NeRF | Improved scene parameterization |
| MARS | NeRF | Modular (separate static/dynamic rendering) |
| [[emernerf|EmerNeRF]] | NeRF | Self-supervised static/dynamic/flow decomposition |
| [[unisim|UniSim]] | NeRF | Closed-loop evaluation in safety-critical scenarios |
| [[neurad]] | NeRF | Dynamic/static separated by positional embedding |
| [[driving-gaussian|Driving Gaussian]] | 3DGS | Incremental static + composite dynamic Gaussians |
| [[street-gaussians]] | 3DGS | 4D SH appearance, multiple moving objects |
| Periodic Vibration Gaussian | 3DGS | Single formula for static+dynamic |
| [[tclc-gs|TCLC-GS]] | 3DGS | Hybrid LiDAR + camera splatting |
| [[ho-gaussian|HO-Gaussian]] | 3DGS | Combined LiDAR + camera point density |
| [[suds|SUDS]] | NeRF | Large-scale (km-scale, 1700 videos) |
| HGS-Mapping | 3DGS | Hybrid Gaussian (spherical + 3D + 2D types) |

## Limitations & Open Questions Identified

1. **Training time**: Current 3DGS methods still face long training times for large scenes.
2. **Novel view quality**: Quality degrades significantly when camera viewpoint moves far from training trajectory — a critical issue for AV sim where ego must deviate from recorded paths.
3. **Editing realism**: 3DGS editing (actor manipulation) is evolving rapidly but hasn't reached production quality for safety-critical testing.
4. **Integration gap**: No existing system fully connects reconstruction → editing → CARLA/SUMO import → closed-loop testing as a seamless pipeline.
5. **Both NeRF and 3DGS** have advantages: NeRF is slower but more stable for training; 3DGS is faster but faces quality issues at novel viewpoints.

> **Open question:** The paper proposes the 4-step framework conceptually but doesn't demonstrate it end-to-end. Has anyone built this complete pipeline as of 2026?

## Relevance to Our Project

- **Validates our architecture**: The static/dynamic decomposition approach ([[splatad]]'s per-actor Gaussians) aligns with the field's consensus direction.
- **Confirms LiDAR gap**: The paper focuses almost entirely on camera rendering — LiDAR simulation is barely mentioned, reinforcing that [[lidar-simulation]] expertise is a differentiating niche.
- **Editing taxonomy useful**: The 5 editing patterns in Fig. 9 map directly to our Phase 3 roadmap operations.
- **CityGaussian and hierarchical methods**: Worth investigating for scaling beyond single-scene reconstruction.
- **[[tclc-gs|TCLC-GS]] and [[ho-gaussian|HO-Gaussian]]**: Two systems that combine LiDAR + camera data for splatting — relevant competitors/references to [[splatad]].
- **UniSim**: Only NeRF system shown to do closed-loop evaluation — our project should aim to demonstrate this with 3DGS.

## Cross-References
- [[3d-gaussian-splatting]] — core representation reviewed
- [[neurad]] — cited as key AV-specific system
- [[street-gaussians]] — cited for dynamic actor handling
- [[splatad]] — not directly cited (CVPR 2025, may postdate the survey's cutoff) but builds on [[neurad|NeuRAD]] which is covered
- [[scene-editing]] — Section 3.2 and Section 4.1.2 cover editing approaches extensively
- [[sim-to-real-transfer]] — the paper's framework Step 4 addresses this indirectly via algorithm deployment
- [[citygaussian]] — large-scale 3DGS method worth its own page
- [[tclc-gs]] — hybrid LiDAR+camera Gaussian splatting, relevant to our LiDAR work
