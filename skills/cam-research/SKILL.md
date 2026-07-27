---
name: cam-research
description: "Phase 1 — Literature mining, competitive analysis, and algorithm extraction for CAD/CAM development."
version: 0.2.0
author: Hermes Agent
status: active
---

# cam-research — Concrete Research Workflow

**Purpose:** Research papers, patents, and competitive systems to extract the best algorithms and architectural patterns.

## Core Workflow

### Step 1: Read Research Backlog
```
cat docs/research/backlog.md
```
Pick the top 3 P1 (Priority 1) items. These are the most critical open questions.

### Step 2: Dispatch Parallel Search Agents (up to 10)
Each agent searches one topic domain using web search and paper archives.

For each topic, search:
- `arxiv.org` — latest CAM algorithms (adaptive clearing, trochoidal milling, engagement maps)
- Google Patents — novel toolpath strategies
- Documentation of commercial systems (Fusion 360, NX, Mastercam, Hypermill)
- Open-source codebases (FreeCAD Path, OpenCamLib, CadQuery)
- Cutting physics papers (Altintas, Budak, manufacturing ML)

### Step 3: Extract Findings
For each algorithm found, produce a research document using this EXACT format:

```markdown
# [Topic] — Research Findings
> Date: YYYY-MM-DD
> Source: [paper URL | patent number | product documentation]

## Problem Statement
[What problem does this solve? 2-3 sentences]

## Existing Approaches
1. **[Approach A]** — from [source]
   - Key insight:
   - Strengths:
   - Weaknesses:

2. **[Approach B]** — from [source]
   - Key insight:
   - Strengths:
   - Weaknesses:

## Recommended Approach
[Which approach to implement, with justification. Reference specific lines or algorithms.]

## Mathematical Formulation
[The math, in clear notation. Include key equations.]

## Implementation Notes
[Edge cases, numerical stability concerns, optimization opportunities, known pitfalls]

## Test Vectors
[Specific inputs and expected outputs that prove the implementation is correct]
- Input: [exact parameter values]
- Output: [exact expected result]
```

### Step 4: Verify Against SDF Kernel
After writing research, confirm the kernel still works:
```bash
python -c "from kernel.types import Vec3; from kernel.sdf import sphere; print(sphere(5.0)(Vec3(0,0,0)))"
# Should print: -5.0
python -m pytest tests/ -q
# Should print: 46 passed
```

### Step 5: Update Backlog
- Mark completed items
- Add new items discovered during research
- Flag any architecture change proposals if research contradicts current design

## Output Path
- Research documents: `docs/research/[topic_slug]_YYYY-MM-DD.md`
- Backlog: `docs/research/backlog.md`
- Architecture changes: `docs/plans/ARCHITECTURE_CHANGE_[id].md`

## Toolset
web, file, delegation, memory

## Profile
cam-research (Gemini 2.5 Pro — high context for paper analysis)

## First Run Priority Topics
1. **SDF octree acceleration** — Search for adaptive sparse grid SDF storage (nano-vdb, OpenVDB patterns)
2. **Constant engagement algorithm** — Papers on engagement angle computation and trochoidal loop generation
3. **GPU voxel operations** — CUDA vs WebGPU for 3D boolean operations on voxel grids
4. **Material cutting parameters** — Extract feeds/speeds data for Al6061, 4140 steel, Ti-6Al-4V
