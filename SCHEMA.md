# Autonomous Driving Simulation Wiki — Schema

This file defines how the LLM maintains this wiki. Read this before any wiki operation.

## Domain
Autonomous driving simulation with focus on: neural scene reconstruction (3D Gaussian Splatting), sensor simulation (camera, LiDAR, radar), sim-to-real transfer, scene editing, and data realism for perception training.

## Directory Structure

```
wiki/
  SCHEMA.md          — this file (conventions, workflows)
  index.md           — content catalog, organized by category
  log.md             — chronological activity record
  raw/               — immutable source documents (papers, articles, notes)
    assets/          — images referenced by sources
  pages/
    concepts/        — technique and concept pages (e.g., 3d-gaussian-splatting.md)
    entities/        — systems, models, datasets, organizations (e.g., splatad.md, nuscenes.md)
    comparisons/     — side-by-side analyses (e.g., lidar-sim-approaches.md)
    sources/         — per-source summary pages (one per ingested paper/article)
  overview.md        — high-level synthesis of the entire domain as understood so far
```

## Page Conventions

### Frontmatter
Every wiki page uses YAML frontmatter:
```yaml
---
title: Page Title
type: concept | entity | comparison | source | overview
created: YYYY-MM-DD
updated: YYYY-MM-DD
tags: [tag1, tag2]
sources: [source-slug-1, source-slug-2]  # which raw sources inform this page
---
```

### Cross-references
Use `[[page-name]]` Obsidian-style wiki links for cross-references between pages. Every page should link to related pages. Orphan pages are a lint failure.

### Source pages
When ingesting a paper/article, create `pages/sources/<slug>.md` with:
- Full citation (authors, title, venue, year, URL/DOI)
- Key contributions (3-5 bullets)
- Technical details relevant to our domain
- Limitations and open questions
- Links to concept/entity pages that this source informs

### Concept pages
Explain the technique/idea, its variants, how it applies to AV simulation, key papers, and open problems. Link to entity pages that implement it.

### Entity pages
Cover a specific system, model, dataset, or organization. Include: what it is, key capabilities, architecture (if relevant), how it relates to other entities, strengths/weaknesses.

### Comparison pages
Side-by-side analysis of approaches. Use tables where appropriate. Always state evaluation criteria and which approach wins on each axis.

## Workflows

### Ingest a source
1. User drops a document into `raw/` (or provides a URL).
2. LLM reads the source.
3. LLM creates `pages/sources/<slug>.md`.
4. LLM updates or creates relevant concept and entity pages.
5. LLM updates `index.md` with new/modified pages.
6. LLM appends to `log.md`.

### Answer a query
1. LLM reads `index.md` to find relevant pages.
2. LLM reads those pages and synthesizes an answer.
3. If the answer is substantial and reusable, file it as a new wiki page (concept, comparison, or ad-hoc).
4. Update `index.md` and `log.md`.

### Lint
1. Check for orphan pages (no inbound links).
2. Check for stale content (sources list concepts not yet having their own page).
3. Check for contradictions between pages.
4. Suggest missing pages or sources to investigate.

## Style
- Write for a senior ML/robotics engineer audience.
- Be precise about what is claimed vs. speculated.
- Cite sources by `[[source-slug]]` link.
- Prefer concrete numbers (PSNR, FID, AP) over vague claims.
- Flag open questions and contradictions explicitly with `> **Open question:**` callouts.
