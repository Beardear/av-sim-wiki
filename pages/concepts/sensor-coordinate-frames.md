---
title: Sensor Coordinate Frames
type: concept
created: 2026-04-11
updated: 2026-04-11
tags: [sensor, calibration, coordinate-system, camera, lidar, radar]
sources: [caesar2019-nuscenes]
---

# Sensor Coordinate Frames

AV datasets use multiple coordinate frames because different sensor types inherit conventions from different fields. Understanding these frames is essential for correct projection, fusion, and simulation rendering.

## The Two Conventions

### Camera frame (Computer Vision / OpenCV)

```
        Z (forward, depth / optical axis)
       /
      /
     O -------→ X (right)
     |
     |
     ↓
     Y (down)
```

- **Origin**: camera optical center.
- **X** → right, **Y** → down, **Z** → forward (into the scene).
- **Why Y is down**: designed to be consistent with image pixel coordinates, where the origin is at the top-left and row index increases downward. When projecting a 3D point via the camera intrinsic matrix K, X maps directly to pixel column (u) and Y maps to pixel row (v) with no sign flip. This is the standard pinhole camera model convention used by OpenCV and virtually all vision libraries.

### Vehicle / LiDAR / Radar frame (Robotics / Automotive)

```
     Z (up)
     |
     |
     O -------→ X (forward)
    /
   /
  Y (left)
```

- **Origin**: sensor center (or ego-vehicle rear axle center for the ego frame).
- **X** → forward, **Y** → left, **Z** → up.
- **Why Z is up**: follows the right-hand rule with the ground plane as XY — physically intuitive for vehicles. Standard in ROS, most AV stacks, and the nuScenes devkit Box class (`"Convention: x points forward, y to the left, z up"`).

## How nuScenes Implements This

In [[nuscenes]], each sensor's data lives in its **native frame**. The devkit's `calibrated_sensor` table stores a rotation quaternion + translation vector per sensor, defining the transform from sensor frame to ego-vehicle frame. The ego-vehicle frame uses the robotics convention.

The transformation chain is:

```
sensor frame  →  ego-vehicle frame  →  global frame
         (calibrated_sensor)      (ego_pose)
```

The rotation in `calibrated_sensor` for a camera encodes the ~90° axis reorientation (CV → robotics) as part of the extrinsic calibration. Code that projects LiDAR points into a camera image must apply the inverse of this chain, and the axis convention difference is handled automatically.

### nuScenes sensor-specific conventions (from devkit source)

| Frame | X | Y | Z | Convention |
|-------|---|---|---|------------|
| Camera | Right | **Down** | Forward | Computer Vision (OpenCV) |
| LiDAR | Forward | Left | **Up** | Robotics |
| Radar | Forward (`"x is front"`) | Left (`"y is left"`) | Up | Robotics |
| Ego vehicle | Forward | Left | Up | Robotics |
| Global | East | North | Up | Map |
| 3D Box | Forward | Left | Up | Robotics |

### Radar velocity convention

Radar returns include `vx, vy` (raw velocity) and `vx_comp, vy_comp` (ego-motion compensated). These are in the radar's native frame: `vx` is radial velocity along X (forward), `vy` is lateral.

## Why This Matters for Simulation

When rendering simulated sensor data from a [[3d-gaussian-splatting]] or NeRF scene:

1. **Camera rendering** must output images in the CV convention (Y-down) so they match real data that downstream models expect.
2. **LiDAR rendering** (e.g., [[splatad]]'s ray-Gaussian intersection) must produce point clouds in the robotics convention (Z-up) to match what [[centerpoint]] / [[pointpillars]] expect.
3. **Sensor fusion** during evaluation (projecting LiDAR points onto camera images, or vice versa) requires the full extrinsic chain including the convention change.
4. **Virtual sensor rigs** for novel-view synthesis must replicate the exact extrinsic transforms from the dataset's `calibrated_sensor` table, including the convention-crossing rotation.

Getting this wrong silently flips or rotates data, which does not crash but produces garbage reconstructions or detections.

## Related

- [[nuscenes]] — primary dataset using these conventions
- [[kitti]] — uses a similar but not identical camera convention (right-down-forward); LiDAR is forward-left-up
- [[waymo-open-dataset]] — uses its own frame conventions (vehicle frame: X-forward, Y-left, Z-up; camera: same as CV convention)
- [[gps-imu]] — ego-pose output that defines the ego-vehicle frame
- [[lidar-simulation]] — rendered LiDAR must match the target convention
- [[caesar2019-nuscenes]] — source paper
