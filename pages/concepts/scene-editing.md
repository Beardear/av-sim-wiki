---
title: Scene Editing
type: concept
created: 2026-04-07
updated: 2026-04-07
tags: [scene-editing, safety, actor-manipulation]
sources: [zhou2026-av-scenario-review]
---

# Scene Editing for AV Simulation

## Why It Matters
Safety validation requires testing scenarios that rarely occur in collected data — near-misses, jaywalkers, red-light runners. Scene editing enables generating these scenarios by modifying reconstructed real-world scenes: removing actors, inserting new ones, changing trajectories.

## Core Operations

### Actor Removal
Delete an actor from the scene and fill the resulting gap (the area that was occluded by the actor).
- In 3DGS: delete Gaussians with the actor's `id` tag.
- Challenge: the background behind the removed actor was never observed → must be **inpainted**.
- [[gs-roadpatching]]: SIGGRAPH Asia 2025 approach specifically for road surface inpainting after vehicle removal.

### Actor Insertion
Place an actor from a different scene (or a synthetic asset) into the target scene.
- **Cross-scene transfer**: Extract an actor's Gaussian set from scene A, transform and insert into scene B. [[astrosplat]] demonstrates this (claims 10^4x better than SplatAD's native insertion).
- **Appearance harmonization**: Inserted actor must match target scene's lighting, exposure, and color palette.

### Trajectory Modification
Change an existing actor's path through the scene.
- In [[splatad]]: modify the per-frame rigid transforms applied to the actor's Gaussians.
- Requires plausible motion models to avoid physically impossible trajectories.

### Multi-Sensor Consistency
All edits must be consistent across camera AND LiDAR. Removing a car must remove it from both the image and the point cloud. Inserted actors must appear in both modalities with consistent geometry.

## Key Systems
| System | Capabilities |
|--------|-------------|
| [[splatad]] | Actor decomposition, removal (no inpainting), trajectory modification |
| [[gaussian-editor]] | Text-guided fine-grained geometry and texture editing |
| [[gaussian-grouping]] | Semantic Gaussian grouping for structured object editing |
| [[neural-scene-graph]] | Foundational actor decomposition via NeRF scene graphs |
| [[gs-roadpatching]] | Road inpainting after object removal |
| [[astrosplat]] | Cross-scene actor transfer |
| IDSplat | Instance-decomposed 3DGS for driving |
| Industrial-Grade Sensor Sim | Full pipeline: reconstruction + editing + multi-sensor rendering |

## 5 Editing Patterns (from [[zhou2026-av-scenario-review]])
The survey identifies five fundamental 3DGS scene editing operations for AV:
1. **Oversample + rotate**: Duplicate and rotate dynamic actors to create new traffic configurations
2. **Move actors**: Relocate dynamic actors to different positions in the scene
3. **Delete dynamic from composite**: Remove dynamic actors, leaving static background intact
4. **Delete dynamic from full scene**: Remove actors from a fully dynamic reconstruction
5. **Move static elements**: Relocate static scene elements (e.g., barriers, signs)

## Evaluation
- **Visual realism**: FID between edited and real scenes
- **Perception consistency**: Detection AP on edited scenes should be comparable to unedited
- **Multi-sensor coherence**: Detections from camera and LiDAR should agree on edited actors

> **Open question:** How to handle shadows and reflections of removed/inserted actors? These are view-dependent effects baked into the Gaussian representation.

## Related
- [[3d-gaussian-splatting]] — editable representation
- [[splatad]] — dynamic actor decomposition
- [[lidar-simulation]] — LiDAR consistency for edits
- [[av-sim-framework]] — editing is Step 2 of the 4-step pipeline
- [[emernerf]] — self-supervised decomposition alternative to annotated actor IDs
