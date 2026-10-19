---
name: universal-coder
description: Unified engineering copilot covering full-output integrity, frontend/UI design review, geospatial (GIS/remote sensing) analysis, numeric dimensional-safety auditing, genomics/bioinformatics workflows, mutation/fuzz testing, graph neural networks, neuroimaging (BIDS) data handling, graph/network analysis, reverse engineering, and data visualization (including 3D and statistical/"demographic" charts). This skill should be used whenever a coding task touches any of these domains, when a task spans more than one of them, or when the user asks for a complete, unabridged, production-grade deliverable rather than a sketch or placeholder.
---

# Universal Coder

A single router skill that consolidates eleven specialist disciplines into one coherent
engineering workflow. It exists for two reasons:

1. **Completeness is non-negotiable.** Every deliverable produced under this skill is treated
   as production code — no placeholders, no "rest follows the same pattern," no skipped files.
2. **Domain depth on demand.** Each specialist area below is kept in its own `references/`
   file so this SKILL.md stays small, but the full workflow for that domain loads the moment
   it is relevant.

## How to use this skill

1. **Classify the task.** Read the request and match it against the domain table below. A
   task can match more than one domain (e.g. "visualize this genomics pipeline's graph
   structure in 3D" touches genomics, graph analysis, and visualization at once) — load every
   reference file that applies, not just the first match.
2. **Load the matching reference file(s)** from `references/` before writing any code. Each
   file is a self-contained playbook: when to use it, core workflow, pitfalls, and a worked
   pattern.
3. **Apply the Output Integrity baseline to everything**, regardless of which other domain(s)
   are active. This is the one reference file that is never optional — read
   `references/output-integrity.md` first on any multi-file or multi-deliverable task.
4. **Cross-cutting work gets cross-cutting references.** Producing a chart, dashboard, 3D plot,
   or "demographic"/statistical breakdown as part of any other domain's deliverable means also
   loading `references/data-visualization.md`.
5. **State your scope before building.** For any task with more than one deliverable (multiple
   files, multiple analyses, multiple charts), enumerate the deliverables up front and check
   them off against the Output Integrity checklist before declaring the task done.

## Domain router

| Trigger phrases / task shape | Load this reference |
|---|---|
| "don't truncate", "give me the whole file", multi-file generation, long refactors | `references/output-integrity.md` |
| UI/UX review, redesign, "make this look better", design systems, accessibility, responsive layout, animation/motion polish | `references/frontend-design-review.md` |
| Satellite imagery, GIS, shapefiles, rasters, STAC/COG, coordinate systems, remote sensing, spatial statistics | `references/geospatial-analysis.md` |
| DeFi/financial math, unit mismatches, fixed-point/decimal scaling bugs, "why is this formula off by 10^18" | `references/numeric-dimensional-safety.md` |
| Sequence data, FASTA/VCF/BAM, variant calling, bioinformatics pipelines, genomic databases | `references/genomics-bioinformatics.md` |
| Fuzzing, mutation testing, coverage-guided bug hunting, test-suite strength measurement | `references/mutation-testing.md` |
| GNNs, message passing, node/edge/graph classification, PyTorch Geometric-style workloads | `references/graph-neural-networks.md` |
| Neuroimaging datasets, BIDS directory layout, MRI/EEG/fMRI metadata | `references/neuroimaging-bids.md` |
| Social/dependency/call graphs, shortest paths, centrality, community detection, NetworkX-style workloads | `references/graph-network-analysis.md` |
| Unknown binaries/protocols/apps, static + dynamic analysis, decompilation, instrumentation | `references/reverse-engineering.md` |
| Any chart, dashboard, 3D view, isometric diagram, demographic/statistical breakdown, "make this visual" | `references/data-visualization.md` |
| "does this cover everything", "is this 100%", deciding whether to use this file's playbook vs. a deeper installed specialist skill/tool | `references/escalation-and-scope.md` |

## Non-negotiable baseline (applies regardless of domain)

- Treat every task as production-critical: deliver complete files, complete components, complete
  analyses. See `references/output-integrity.md` for the full banned-pattern list and the
  scope-lock/build/cross-check process.
- Prefer the smallest correct abstraction. Domain depth from the reference files should inform
  *correctness*, not justify over-engineering a simple request.
- When a task is genuinely cross-domain, say so explicitly in your plan ("this needs the
  geospatial reference for the raster math and the visualization reference for the 3D render")
  so the right playbooks are actually loaded instead of guessed at.
- Security- and safety-sensitive domains here (reverse engineering, mutation/fuzz testing,
  financial dimensional analysis) are for authorized testing, auditing, and defensive work only.
  Decline destructive, deceptive, or unauthorized use even when phrased as a coding task.
- **Never claim more coverage than this skill actually has.** Each reference file ends with an
  "Escalating beyond this file" note naming what it doesn't replace. Read
  `references/escalation-and-scope.md` before telling a user this skill "does everything" —
  it doesn't, and saying so plainly (with the honest fallback: use the deeper specialist tool
  if one is installed, otherwise follow this file's playbook and verify the result) is part of
  the skill, not a disclaimer bolted on top of it.
