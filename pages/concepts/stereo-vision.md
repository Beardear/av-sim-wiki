---
title: Stereo Vision
type: concept
created: 2026-04-07
updated: 2026-04-07
tags: [stereo, depth, geometry, cameras]
sources: [gemini-nerf-gs-fundamentals]
---

# Stereo Vision

## What It Is
Depth estimation from two simultaneous images captured by cameras with a known baseline (physical separation). Uses triangulation geometry to compute per-pixel depth:

**depth = (focal_length x baseline) / disparity**

Where disparity = pixel shift between left and right images for the same 3D point.

## Rectification
Real cameras are never perfectly aligned. **Rectification** warps both images so:
- Optical axes are perfectly parallel
- Epipolar lines become horizontal
- A feature at row N in the left image is guaranteed to appear at row N in the right image

This reduces stereo matching from a 2D search (expensive, O(W*H)) to a 1D horizontal scan per row (fast, O(W)). This speedup makes real-time stereo depth estimation practical.

## Why Stereo Matters for AV
- Provides **absolute scale** — monocular cameras cannot distinguish a toy car at 1m from a real car at 100m
- Enables dense depth maps without active sensors (LiDAR-free depth)
- [[kitti]] uses stereo cameras as a primary sensing modality

## KITTI's Dual Camera Design
KITTI uses both monochrome and color stereo pairs:
- **Monochrome**: No Bayer filter → 100% light transmission, no demosaicing blur. Best for geometric tasks (stereo matching, optical flow, visual odometry) requiring sub-pixel accuracy
- **Color**: Bayer filter blocks ~2/3 of light and introduces interpolation blur. Best for semantic tasks (object detection, tracking) requiring color context (red vs green light, stop signs)

## Related KITTI Benchmark Tasks
- **Stereo**: Dense depth from disparity
- **Optical flow**: Per-pixel motion vectors between consecutive frames
- **Visual odometry**: Camera trajectory estimation from feature matching across frames
- **3D object detection**: Place 3D bounding boxes in metric space (requires depth)
- **3D tracking**: Associate detections over time with unique IDs + velocity

## Related
- [[kitti]] — stereo-centric AV benchmark
- [[structure-from-motion]] — stereo provides scale for SfM
- [[sensor-fusion]] — stereo depth as camera-based depth input
