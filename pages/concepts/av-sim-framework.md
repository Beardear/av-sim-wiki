---
title: AV Simulation Framework (4-Step Pipeline)
type: concept
created: 2026-04-07
updated: 2026-04-07
tags: [framework, pipeline, architecture]
sources: [zhou2026-av-scenario-review]
---

# AV Simulation Framework — 4-Step Pipeline

Proposed by [[zhou2026-av-scenario-review]] as a reference architecture for building complete AV test systems using neural scene reconstruction.

## Step 1: Large-Scale Dynamic Scene Reconstruction
Reconstruct real driving scenes from multi-sensor logs using NeRF or [[3d-gaussian-splatting]].
- Inputs: multi-camera images, LiDAR point clouds, sky masks
- Decompose into static background + dynamic traffic participants
- Output: renderable scene representation

## Step 2: Free Editing of Scenarios
Modify the reconstructed scene to create safety-critical test cases.
- **5 editing patterns** (for 3DGS):
  1. Oversample + rotate dynamic actors
  2. Move dynamic actors to new positions
  3. Delete dynamic actors (keep static background)
  4. Delete dynamic actors from full dynamic scene
  5. Move static elements
- See [[scene-editing]] for detailed techniques

## Step 3: Import to Simulation Software
Bridge reconstructed/edited scenes to existing sim platforms.
- Export to CARLA or SUMO format
- Ensure rendered sensor data matches expected formats
- Validate that simulation behavior matches the reconstructed scene

## Step 4: Algorithm Deployment & Testing
Deploy perception and decision-making algorithms against the reconstructed scenarios.
- Perception testing (detection, tracking, segmentation)
- Decision-making validation
- Closed-loop evaluation (ego agent interacts with scene)

## Status
This framework is **conceptual** — no existing system implements all 4 steps end-to-end as a seamless pipeline. Our project's roadmap (Phases 1–3) covers Steps 1–2, with Step 3–4 as stretch goals.

> **Open question:** What's the minimal viable version of Step 3? Can we skip CARLA/SUMO and evaluate perception models directly on rendered sensor data?

## Related
- [[splatad]] — our system for Step 1
- [[scene-editing]] — Step 2 techniques
- [[sim-to-real-transfer]] — Step 4 evaluation
- [[unisim]] — closest existing system to implementing Steps 1+4
