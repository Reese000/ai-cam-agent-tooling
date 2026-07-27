---
name: cam-documentation
description: "Phase 2 — Module contract writing, API spec generation, schema registry maintenance."
version: 0.2.0
author: Hermes Agent
status: active
---

# cam-documentation — Concrete Documentation Workflow

**Purpose:** Write module contracts BEFORE any implementation code exists.

## Core Workflow

### Step 1: Read Research Outputs
```
ls docs/research/*.md
cat docs/module_contracts/TEMPLATE.md
```
Read all research documents from Phase 1 to understand what needs to be built.

### Step 2: Write Module Contracts (one per module)

For each module in the architecture, create `docs/module_contracts/[module].md` using the template.

**Priority order** (most foundational first):
1. `kernel/sdf.md` — SDF primitives, booleans, transforms, octree
2. `cam/adaptive_clearing.md` — Constant engagement toolpath engine
3. `simulation/voxel.md` — Voxel material removal engine
4. `simulation/kinematics_3axis.md` — 3-axis kinematic chain
5. `post/engine.md` — Post processor event-driven engine
6. `optimizer/feed_speed.md` — MRR-based feed/speed optimizer
7. `cam/trochoidal.md` — Trochoidal slotting strategy
8. `cam/hsm_profiling.md` — HSM adaptive profile finishing

### Each Contract Must Include:

1. **Public API** — Every function signature with argument names, types, return types
   ```python
   def adaptive_clearing(
       stock_sdf: SDF,
       part_sdf: SDF,
       tool: ToolDef,
       stepdown: float,
       max_engagement_angle: float = 35.0,
       tolerance: float = 0.01
   ) -> tuple[list[ToolpathSegment], EngagementMap]:
   ```

2. **Data schemas** — Input/output formats as Pydantic or dataclass definitions

3. **State management** — Is the module stateless? If stateful, how is state initialized?

4. **Performance contract** — Max execution time and memory for standard cases

5. **Error conditions** — Every exception class and when it's raised

6. **Test specification** — 3-5 test cases that must pass (normal, edge, error)

### Step 3: Verify Contracts Are Self-Consistent

Run a consistency check:
```bash
# Verify all contracts reference types that actually exist
python -c "
from kernel.types import Vec3, BBox, Transform, SDF, Mesh, ToolDef
from kernel.cam_types import StockModel, MDP
print('Contract types exist: OK')
"
```

### Step 4: Update Architecture Diagram

Append to `docs/architecture.md`:
```markdown
## Module: [name]
- Status: CONTRACTED | IN DEVELOPMENT | TESTED | COMPLETE
- Contract: docs/module_contracts/[name].md
- Depends on: [list]
- Used by: [list]
```

## Contract Template Location
`docs/module_contracts/TEMPLATE.md`

## Output Path
`docs/module_contracts/[module_name].md`

## Toolset
file, terminal

## Profile
cam-docs (Claude Sonnet 4)
