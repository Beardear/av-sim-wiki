---
title: "Gemini Q&A — Depth Maps, Floaters, Datasets, Open-Source Frameworks"
type: source
created: 2026-04-07
updated: 2026-04-07
tags: [depth-map, floaters, phantom-braking, nuscenes, argoverse, carla, mmdetection3d]
sources: []
---

# Gemini Q&A — Depth Maps, Floaters, Datasets, Open-Source Frameworks

## Citation
- **Format**: Gemini AI conversation (20 messages, AV-relevant subset)
- **Source**: Gemini web app, clipped 2026-04-07
- **Topics**: Virtual camera depth maps, GS floaters/phantom braking, dataset comparison, synthetic vs real data, open-source AV frameworks

## Key Contributions
- Explains the "floater" problem in 3DGS and its safety implications (phantom braking)
- Compares AV datasets (KITTI, nuScenes, Waymo, Argoverse 2) for project suitability
- Covers open-loop vs closed-loop testing and the role of synthetic data
- Surveys key open-source AV frameworks

## Technical Details

### Virtual Camera Depth Maps from GS
- GS rasterizer returns multiple tensors: `render_package["render"]` (RGB, [3,H,W]) and `render_package["depth"]` (distance in meters, [1,H,W])
- Depth maps saved as `.npy` (float32, exact meters) rather than images (limited to 255 values)
- Depth maps enable cross-sensor validation: compare camera depth with LiDAR range at same pixel

### The Floater Problem
- During training, GS may place semi-transparent Gaussians in empty space to reproduce view-dependent effects (reflections, specularities)
- **Camera** sees the floater and reports an obstacle (e.g., 12.5m)
- **LiDAR** (Monte Carlo ray-drop) recognizes low physical density and passes through → reports true surface (e.g., 40.0m)
- This sensor disagreement would cause **phantom braking** in a real AV
- LiDAR is correct in this case — it measures physical geometry, not optical appearance
- Floaters are actually useful for simulation: they reproduce real-world optical illusions that sensor fusion must handle

### Dataset Comparison for AV Projects
| Dataset | Sensors | Strengths | Limitations |
|---------|---------|-----------|-------------|
| [[kitti]] | 2 stereo pairs, 1 LiDAR, GPS/IMU | Simple, fast prototyping | Legacy, 1 front view, no radar |
| [[nuscenes]] | 6 cameras, 1 LiDAR, 5 radars | 360-degree, includes radar, rain/night | Lower LiDAR density (32-beam) |
| [[waymo-open-dataset]] | 5 cameras, 5 LiDARs | Highest fidelity, 10Hz labels | Large, complex to process |
| [[argoverse]] | Cameras, 310m-range LiDAR | Long-range sensing, HD vector maps | Smaller community |

### Open-Loop vs Closed-Loop
- **Open-loop (real data)**: Read-only replay. Cannot test "what if the car braked?"
- **Closed-loop (synthetic)**: Actions affect the simulation. Essential for testing planning/control algorithms
- **Long-tail testing**: Synthetic engines can programmatically create rare edge cases (jaywalkers, falling debris)

### Open-Source AV Frameworks
- **[[mmdetection3d]]**: Perception & sensor fusion (CenterPoint, BEVDet, PointPillars implementations)
- **[[carla]]**: Unreal Engine closed-loop simulator with physics, traffic, weather, Python API
- **[[autoware]]**: Full AD stack on ROS2 (sensing → perception → planning → control)
- **[[open3d]]**: 3D geometry processing library (KD-trees, voxel downsampling, ICP, point cloud visualization)

## Links to Wiki Pages
- [[3d-gaussian-splatting]] — floater problem context
- [[lidar-simulation]] — LiDAR as physical reality check
- [[nuscenes]], [[kitti]], [[waymo-open-dataset]], [[argoverse]] — datasets
- [[carla]], [[autoware]], [[mmdetection3d]], [[open3d]] — frameworks
