---
title: "Gemini Q&A — Radar/Doppler in GS, Dynamic Object Simulation, BEV"
type: source
created: 2026-04-07
updated: 2026-04-07
tags: [radar, doppler, rcs, dynamic-objects, bev, 3dgs]
sources: []
---

# Gemini Q&A — Radar/Doppler in GS, Dynamic Object Simulation, BEV

## Citation
- **Format**: Gemini AI conversation (20 messages, AV-relevant subset)
- **Source**: Gemini web app, clipped 2026-04-07
- **Topics**: Ego-induced Doppler in static GS, dynamic object handling, RCS, compositional scene decomposition, BEV representation

## Key Contributions
- Explains the Doppler limitation of static 3DGS for radar simulation: only ego-induced Doppler is meaningful; dynamic actors are "baked in" as static geometry
- Details the compositional approach used by AV companies to handle dynamic objects in GS
- Covers Radar Cross Section (RCS) and its significance for AV radar
- Explains Bird's Eye View (BEV) as the industry-standard representation for sensor fusion

## Technical Details

### Doppler in Static 3DGS
- Standard 3DGS freezes a dynamic world into a static diorama
- Ego motion still produces valid Doppler shifts against static objects (physically meaningful — real radar sees radial velocity of static objects)
- Moving actors baked into static geometry → simulated Doppler only reflects ego motion, not true actor velocity

### Compositional Scene Decomposition (Industry Solution)
1. **Masking & tracking**: Perception stack identifies dynamic objects with 3D bounding boxes
2. **Static background**: Masked pixels/points used to train background 3DGS
3. **Rigid body actors (vehicles)**: Object-centric Gaussian models in local coordinates; apply SE(3) transforms per frame
4. **Non-rigid actors (pedestrians)**: Deformation MLPs predict position/covariance offsets conditioned on time (4D GS), or skeleton-driven splatting via SMPL model

### Radar Cross Section (RCS)
- Measured in dBsm (decibels relative to 1 m^2)
- Depends on material (metal >> human tissue), shape (corner reflectors amplify), and size
- Typical values: semi-truck 10-100 m^2, car 1-10 m^2, pedestrian 0.1-1 m^2
- Challenge: truck echo can be 1000x stronger than nearby pedestrian, "washing out" the weak return

### Bird's Eye View (BEV)
- Top-down 2D projection of 3D sensor data
- Cars drive on a 2D plane → BEV captures the relevant spatial relationships
- Enables sensor fusion by projecting camera, LiDAR, and radar data onto a unified 2D grid
- Reduces 3D processing to efficient 2D image processing (compatible with 2D CNNs)
- Industry standard: BEVFormer, BEVDet, CenterPoint all operate in BEV space

## Links to Wiki Pages
- [[radar-simulation]] — radar simulation concept page
- [[bird-eye-view]] — BEV concept page
- [[scene-editing]] — dynamic object handling
- [[3d-gaussian-splatting]] — underlying representation
- [[sensor-fusion]] — BEV as fusion representation
