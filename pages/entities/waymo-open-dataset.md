---
title: Waymo Open Dataset
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [dataset, multi-sensor, benchmark, large-scale]
sources: []
---

# Waymo Open Dataset

## What It Is
A large-scale autonomous driving dataset released by Waymo. One of the most comprehensive multi-sensor AV datasets available, alongside [[nuscenes]].

## Sensor Suite
- 5 LiDAR sensors (1 top + 4 side-mounted)
- 5 cameras (front + side)
- High-resolution, high frame rate

## Scale
- 1000+ driving segments (each ~20 seconds)
- 12M+ 3D bounding box labels
- Diverse conditions: day, night, rain, urban, suburban, highway

## Why It's Relevant
- Major benchmark for 3D detection and tracking
- Higher-resolution LiDAR than [[nuscenes]] (64-beam top LiDAR vs. nuScenes' 32-beam)
- Many neural reconstruction papers evaluate on Waymo in addition to nuScenes
- Waymo is a key target employer for AV simulation roles

## Status in Our Project
Not currently used. nuScenes is our primary dataset due to better neurad-studio dataparser support. Waymo support could be added later.

> **Open question:** Does neurad-studio have a Waymo dataparser, or would one need to be written?

## Related
- [[nuscenes]] — our primary dataset, similar scope
- [[kitti]] — smaller/older predecessor
- [[splatad]] — could potentially train on Waymo data
