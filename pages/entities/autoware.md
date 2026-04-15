---
title: Autoware
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [framework, full-stack, ros2, open-source]
sources: [gemini-depth-maps-datasets]
---

# Autoware

## What It Is
The world's leading open-source full-stack autonomous driving framework, built on ROS2 (Robot Operating System 2). Covers the entire AV pipeline: sensing → perception → planning → control.

**Repository**: `autowarefoundation/autoware`

## Key Capabilities
- Production-grade C++ codebase
- Modular architecture: each pipeline stage is a separate ROS2 node
- Supports real hardware deployment (not just simulation)
- Integrates with [[carla]] for closed-loop virtual testing

## Pipeline Stages
1. **Sensing**: Sensor drivers, calibration, synchronization
2. **Perception**: 3D detection, tracking, prediction (can use [[mmdetection3d]] models)
3. **Planning**: Route planning, behavior planning, motion planning
4. **Control**: Trajectory following, vehicle interface

## Role in AV Simulation
- Consume synthetic sensor data from [[carla]] or [[splatad]] through ROS2 topics
- Test full planning/control stack against simulated environments
- Validate that simulated data is realistic enough to drive the full stack correctly

## Related
- [[carla]] — provides simulated sensor data to Autoware
- [[sensor-fusion]] — implemented within Autoware's perception module
- [[av-sim-framework]] — Autoware represents the downstream consumer of simulated data
