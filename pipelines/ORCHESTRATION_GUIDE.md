# AI CAM 3.0 — Orchestrator Execution Guide

> **For the orchestrator agent (profile: cam-orch).**
> How to execute the 7-phase autonomous pipeline.

## Pre-Flight Check

Before starting a cycle, verify:
1. `git status` — working tree clean, no uncommitted changes
2. `python -m pytest tests/ -q` — all tests pass on current state
3. Kernel `kernel/types.py` + `kernel/sdf.py` compile without errors
4. Module contracts exist for modules to be changed this cycle
5. `hermes profile list` — all 8 profiles registered

## Pipeline Execution

### Trigger Full Cycle

```
hermes cron create "0 3 * * *" \
  --profile cam-orch \
  --skills cam-orchestration \
  --prompt "Execute the AI CAM 3.0 full development cycle per agent/pipelines/full-cycle.json. Start with Phase 1 (Research), cascade to Phase 7. Report status to #cam-updates."
```

### Manual Phase Start

```
hermes --profile cam-research --skills cam-architecture-canvas,cam-research \
  chat -q "Resolve the top 3 items in docs/research/backlog.md. Dispatch 50 parallel subagents. Save findings to docs/research/."
```

## Phase Transition Protocol

Each phase reports one of:
- **PASS** → proceed to next phase
- **FAIL with retry** → dispatch failure diagnosis, retry with diversity injection
- **FAIL critical** → pause pipeline, escalate to human (send message), commit current state

## Architecture Change Protocol

Any agent can submit a change proposal using `docs/plans/ARCHITECTURE_CHANGE_TEMPLATE.md`.
Submit to: `docs/plans/ARCHITECTURE_CHANGE_[id]_[date].md`

Approval:
- Minor: orchestrator approves
- Major: orchestrator + critic review
- Foundational: must survive 2 full cycles without regression

## Rollback

```bash
git revert HEAD --no-edit && git commit -m "rollback: [reason]"
```

Always revert before retrying. Never build on top of broken state.

## First Run Targets

The first run should target:
- Phase 1: Research P1 backlog items (SDF octree, engagement algorithm, GPU voxel)
- Phase 2: Module contracts for cam/ and simulation/
- Phase 3: Implementation plan for cam/strategies/adaptive_clearing.py
- Phase 4: Implement adaptive clearing engine
- Phase 5: Test with golden NIST test part
- Phase 6: Full audit
- Phase 7: Critic review
