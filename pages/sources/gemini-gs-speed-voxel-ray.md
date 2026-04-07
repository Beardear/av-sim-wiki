---
title: "Gemini Q&A — GS Speed Advantage, Voxel vs Ray Driven"
type: source
created: 2026-04-07
updated: 2026-04-07
tags: [3dgs, nerf, rendering, voxel, ray-driven]
sources: []
---

# Gemini Q&A — GS Speed Advantage, Voxel vs Ray Driven

## Citation
- **Format**: Gemini AI conversation (8 messages)
- **Source**: Gemini web app, clipped 2026-04-07
- **Topics**: Why GS is faster than NeRF, voxel-driven vs ray-driven rendering, application examples

## Key Contributions
- Breaks down the 3 core reasons GS achieves 100+ FPS vs NeRF's <1 FPS
- Clarifies that GS is **not** voxel-driven — it is an unstructured, continuous point-based representation
- Compares voxel-driven vs ray-driven paradigms with AV-relevant application examples

## Technical Details

### Why GS is Faster
1. **Tile-based rasterization vs ray marching**: GS projects ("splats") 3D Gaussians to 2D screen, sorts by depth, alpha-blends. This is object-centric and GPU-native. NeRF shoots millions of rays, each requiring hundreds of MLP queries.
2. **Spherical harmonics vs MLP for view dependence**: GS evaluates a fast polynomial (SH coefficients) per Gaussian for view-dependent color. NeRF requires re-evaluating the full MLP for each new viewing direction.
3. **No empty-space computation**: GS only processes existing Gaussians. NeRF rays step through empty space, querying the MLP only to find density=0.

### GS is Not Voxel-Driven
- Voxels: rigid discrete grid, isotropic cubes, memory scales O(N^3)
- GS: unstructured cloud, anisotropic ellipsoids (covariance matrix allows stretch/rotate), memory only where geometry exists
- GS has adaptive density control: cloning in high-detail regions, pruning transparent Gaussians

### Voxel-Driven vs Ray-Driven
| Aspect | Voxel-Driven | Ray-Driven |
|--------|-------------|------------|
| Priority | Physical space | Observer viewpoint |
| Memory | Very high (cubic scaling) | Low-moderate |
| Compute | Low (array lookups) | Very high (intersection tests) |
| AV examples | VoxelNet (3D detection), CT/MRI | NeRF, VFX ray tracing, LiDAR sim |

- Modern hybrids (Instant-NGP, Plenoxels) use sparse voxel grids to skip empty space, then ray march only in occupied voxels

## Links to Wiki Pages
- [[3d-gaussian-splatting]] — GS rendering pipeline details
- [[nerf]] — NeRF rendering comparison
- [[lidar-simulation]] — ray-driven LiDAR simulation
