---
title: UniSim
type: entity
created: 2026-04-07
updated: 2026-04-07
tags: [system, nerf, closed-loop, simulation]
sources: [zhou2026-av-scenario-review]
---

# UniSim

## What It Is
A NeRF-based neural sensor simulation system that enables closed-loop evaluation of autonomous systems in safety-critical scenarios. One of the first systems to demonstrate that neural reconstruction can replace traditional simulation for end-to-end AV testing.

## Key Capabilities
- Closed-loop evaluation (not just open-loop rendering)
- Demonstrated that mixing NeRF-rendered data with real data improves perception model robustness
- Safety-critical scenario generation

## Why It Matters
UniSim represents the end goal of our project's direction — using neural scene reconstruction not just for pretty images but for actual closed-loop AV testing. The key question is whether [[splatad]] (3DGS-based, real-time) can achieve the same closed-loop capability with better performance.

> **Open question:** Has anyone demonstrated closed-loop evaluation with a 3DGS-based system (not NeRF)? SplatAD's real-time rendering should make this more practical.

## Related
- [[neurad]] — also targets AV simulation, NeRF-based
- [[splatad]] — 3DGS alternative aiming for similar goals
- [[sim-to-real-transfer]] — UniSim showed sim data can improve real-world perception
- Source: [[zhou2026-av-scenario-review]] (Table 4)
