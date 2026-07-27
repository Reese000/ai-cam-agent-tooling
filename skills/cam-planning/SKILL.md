---
name: cam-planning
description: "Phase 3 — Task decomposition, dependency graphing, and wave allocation."
version: 0.2.0
author: Hermes Agent
status: active
---

# cam-planning — Concrete Planning Workflow

**Purpose:** Decompose module contracts into bite-sized TDD tasks with dependency ordering.

## Core Workflow

### Step 1: Read Module Contracts
```
ls docs/module_contracts/*.md
```
Read ALL contracts to understand the full scope of work.

### Step 2: Task Decomposition

Break each module into tasks. **Each task = 2-5 minutes of focused agent work.**

**Good task size:**
```
### Task: Implement sphere_fillet()
- Create: kernel/sdf.py:sphere_fillet()
- Test: tests/unit/test_sdf.py
- 15 lines of implementation
- 20 lines of tests
```

**Bad task size:**
```
### Task: Implement all CAM strategies
- Too large — split into one task per strategy
```

### Step 3: Tag Each Task

| Tag | Meaning |
|-----|---------|
| `risk: low` | Well-understood algorithm, clear spec, easy to test |
| `risk: medium` | Novel combination, moderate complexity |
| `risk: high` | Requires research, unproven approach, needs escalation |
| `depends: [task_id]` | Must wait for another task to complete |
| `parallel: true` | Can run concurrently with other parallel tasks |

### Step 4: Build Wave Schedule

```
Wave 0 (Foundation — serial, no agents):
  Task A: Add missing types (done by orchestrator, not agent)
  Task B: Create directory stubs

Wave 1 (Core — parallel, up to 10 agents):
  Tasks that have no dependencies on each other
  Example: sdf_octree(), engagement_map(), gcode_parser()

Wave 2 (Integration — serial agents):
  Tasks that depend on Wave 1
  Example: adaptive_clearing() uses engagement_map()

Wave 3 (Polish — parallel):
  Independent finishing work
  Example: docstrings, type annotations, benchmarks
```

### Step 5: Write Plan File

Save to `docs/plans/[cycle]_[module]_plan.md`:

```markdown
# [Module] Implementation Plan — Cycle [N]

> **For Hermes:** Use cam-development skill with subagent-driven-development.

## Wave Schedule
[Waves with task assignments]

## Tasks
### Task 1: [Name]
**Objective:** [One sentence]
**Files:** [Create/Modify paths]
**Risk:** [low/medium/high]
**Parallel:** true/false
**Depends on:** [task IDs]

Steps:
1. Write failing test
2. Run: pytest [test_path] -v
3. Implement
4. Run: pytest [test_path] -v
5. Run: python -m pytest tests/ -q

## Verification Plan
- [ ] All tasks complete
- [ ] All tests pass
- [ ] Performance benchmark within contract
```

## Output Path
`docs/plans/[cycle]_[module]_plan.md`

## Toolset
file, delegation, todo

## Profile
cam-plan (Claude Sonnet 4)
