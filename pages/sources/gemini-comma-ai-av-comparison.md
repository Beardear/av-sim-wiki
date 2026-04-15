---
title: "Gemini Q&A — comma.ai / openpilot and AV System Comparison"
type: source
created: 2026-04-07
updated: 2026-04-07
tags: [openpilot, comma-ai, waymo, zoox, tesla, adas]
sources: []
---

# Gemini Q&A — comma.ai / openpilot and AV System Comparison

## Citation
- **Format**: Gemini AI conversation (4 messages)
- **Source**: Gemini web app, clipped 2026-04-07
- **Topics**: Why comma.ai open-sources openpilot, comparison of openpilot vs Tesla FSD vs Waymo vs Zoox

## Key Contributions
- Explains comma.ai's open-source strategy as a combined regulatory, engineering, and data-collection play
- Compares L2 consumer ADAS (openpilot, Tesla FSD) vs L4 robotaxis (Waymo, Zoox)

## Technical Details

### comma.ai / openpilot Strategy
- **Regulatory**: Open-sourcing shifts liability from company to driver (user installs software on blank hardware)
- **Crowdsourced car support**: Community reverse-engineers CAN buses, enabling 275+ vehicle support
- **Data flywheel**: Free software incentivizes use; hardware uploads driving telemetry/video → massive training dataset
- **"Android of AD"**: Hardware-agnostic, open-source platform vs Tesla's vertically integrated "Apple" model

### L2 ADAS Comparison: openpilot vs Tesla FSD
- Both converged on end-to-end neural network architecture (pixels → controls)
- **Tesla FSD (v12+)**: Stripped explicit C++ logic for pure deep learning. Superior in complex urban environments (roundabouts, unprotected lefts)
- **openpilot**: Optimized for highway. Smoother, more predictable ride on long stretches. Defers to driver quicker in complex urban scenarios

### L4 Robotaxis: Waymo vs Zoox
- Both use heavy sensor fusion (LiDAR + radar + cameras) in pre-mapped, geofenced environments
- **Waymo**: Retrofitted Jaguar I-Paces, largest commercial driverless fleet, scaling rapidly
- **Zoox**: Custom bidirectional vehicle (no steering wheel), purpose-built for ride-hailing. Smaller operational footprint

## Links to Wiki Pages
- [[comma-ai]] — comma.ai entity page
