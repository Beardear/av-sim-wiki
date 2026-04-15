# NVIDIA Cosmos and Related Platforms - Web Research

Compiled: 2026-04-10. Sources: NVIDIA Newsroom (CES Jan 2025, GTC Mar 2025), arXiv:2501.03575, NVIDIA developer pages, RidgeRun ecosystem explainer.

## Cosmos
Platform of generative World Foundation Models (WFMs) for physical AI. Three families: Predict (world state from text/image/video), Transfer (structured inputs to photoreal video), Reason (chain-of-thought spatiotemporal reasoning). Both autoregressive and diffusion architectures. Cosmos Tokenizer: 8x compression, 12x speed vs competitors. Open-weight on Hugging Face/NGC. Adopters: Uber, Waabi, Wayve, Figure AI, Foretellix, others.

## Omniverse
3D simulation platform on USD. Ray-traced real-time multi-sensor sim (camera, radar, LiDAR) with RTX. Multi-GPU. Foundation for DRIVE Sim and Isaac Sim.

## DRIVE Sim
AV simulation on Omniverse. L2-L5 multi-sensor sim. Includes AlpaSim (open-source, closed-loop testing) and NuRec (neural reconstruction from sensor logs).

## Isaac Sim
Robotics simulation on Omniverse. Manufacturing/logistics focus. Less AV-relevant.
