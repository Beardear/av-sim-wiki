---
title: MMDetection3D
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [framework, perception, sensor-fusion, detection]
sources: [gemini-depth-maps-datasets]
---

# MMDetection3D

## What It Is
OpenMMLab's open-source 3D object detection toolbox. The industry-standard framework for implementing and benchmarking perception and [[sensor-fusion]] models.

**Repository**: `open-mmlab/mmdetection3d`

## Key Capabilities
- Pre-built implementations of major sensor fusion papers: CenterPoint, BEVDet, PointPillars, BEVFusion, VoxelNet
- Supports camera-only, LiDAR-only, and multi-modal detection
- Compatible with [[nuscenes]], [[kitti]], [[waymo-open-dataset]], and [[argoverse]] data formats
- Modular design: swap backbones, heads, and fusion strategies

## Role in AV Simulation Validation
Critical for measuring [[sim-to-real-transfer]]:
1. Train a detector (e.g., CenterPoint) on real data
2. Generate synthetic sensor data from [[splatad]] or [[nerf]]
3. Run the detector on synthetic data
4. Compare detection AP (Average Precision) between real and synthetic inputs
5. The AP gap measures the perception-level domain gap

## Related
- [[sensor-fusion]] — implements fusion architectures
- [[bird-eye-view]] — many models operate in BEV space
- [[sim-to-real-transfer]] — downstream validation tool
