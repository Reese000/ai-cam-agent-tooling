---
name: cam-orchestration
description: "Pipeline orchestration for the full 7-phase autonomous development cycle."
version: 0.2.0
author: Hermes Agent
status: active
---

# cam-orchestration — Execution Guide for the Orchestrator Agent

**Purpose:** Manage the full autonomous development pipeline as a cascading multi-agent workflow.

## Pre-Flight Check

Before starting ANY cycle, verify:
```bash
cd /mnt/c/Users/reese/OneDrive/Desktop/AI/AI CAM 3.0

# 1. Clean working tree
git status | grep "nothing to commit" && echo "CLEAN" || echo "DIRTY"

# 2. Tests pass
python -m pytest tests/ -q 2>&1 | grep "passed" && echo "TESTS OK"

# 3. All imports resolve
python -c "import kernel, cam, simulation, optimizer, post, api; print('IMPORTS OK')"

# 4. Profiles registered
hermes profile list | grep cam- | wc -l
# Should show 8 cam- profiles

# 5. Disk space
df -h /mnt/c/ 2>&1 | tail -1
```

If any check fails: STOP. Fix before proceeding.

## Pipeline Status Tracking

Use `todo` to track which phase is active:
```
Phase 1: Research     [in_progress]
Phase 2: Documentation [pending]
Phase 3: Planning     [pending]
Phase 4: Development  [pending]
Phase 5: Testing      [pending]
Phase 6: Auditing     [pending]
Phase 7: Criticism    [pending]
```

## Phase Transition Decisions

After each phase completes, evaluate the artifacts produced:

### Phase 1 → Phase 2
Check: `ls docs/research/` has at least 3 documents.
Decision: PASS if ≥3 docs exist with correct format. FAIL → diagnose.

### Phase 2 → Phase 3
Check: `ls docs/module_contracts/` has at least 2 contracts.
Decision: PASS if contracts reference types that actually exist in `kernel/types.py`.

### Phase 3 → Phase 4
Check: `ls docs/plans/` has at least 1 plan file.
Decision: PASS if plan has concrete wave assignments and task decomposition <5 min/task.

### Phase 4 → Phase 5
Check: `python -m pytest tests/ -q` — all tests pass.
Decision: PASS if same-or-higher test count as before the cycle.

### Phase 5 → Phase 6
Check: Tests pass + coverage > current baseline.
Decision: PASS if no regressions. FAIL if any test fails — revert development.

### Phase 6 → Phase 7
Check: `ls docs/audit/` has audit report.
Decision: PASS if no critical issues. ISSUES_EXIST → flag for development fix.

### Phase 7 → Complete
Check: Critic report saved.
Decision: PASS if structural verdict is not REJECTED.

## Failure Response Table

| Phase Failure | Symptom | Action |
|---------------|---------|--------|
| Research stalled | No docs after 50 turns | Reduce topic scope, provide more specific search queries |
| Documentation blocked | Can't write contracts | Read the module's existing code first, then write spec |
| Planning blocked | Tasks too large | Split largest task. Recurse until all tasks are <5 min |
| Development fails | Tests fail after implementation | Revert, try different model (diversity injection), reduce scope |
| Testing fails | Performance regression | Profile hot spots, cache identified bottleneck, retry |
| Audit fails | Critical security issue | Stop pipeline, fix immediately, re-audit before proceeding |
| Critic rejects | Structural flaw | Generate architecture change proposal, restart from Phase 1 |

## When to Escalate to Human

- **Pipeline fails at the same phase 3 times in a row** → bug in the pipeline design itself
- **Agent produces clearly malicious code** → check `exec`, `eval`, `os.system` usage
- **All 3 models fail the same task** → task is fundamentally wrong or impossible
- **Audit finds something the system can't auto-fix** → e.g., external dependency bug

## Architecture Change Proposals

Any agent can propose changes using the template:
```
docs/plans/ARCHITECTURE_CHANGE_TEMPLATE.md
```

Save as: `docs/plans/ARCHITECTURE_CHANGE_[id]_[date].md`

Approval:
- Minor changes: orchestrator approves inline
- Major changes: orchestrator + critic review needed
- Foundational: must survive 2 full cycles without regression

## Rollback
```bash
git revert HEAD --no-edit
git commit -m "rollback: [reason]"
```
Always revert before retrying. Never build on broken state.

## Pipeline Configuration
`agent/pipelines/full-cycle.json`

## Toolset
delegation, cronjob, messaging, file, terminal

## Profile
cam-orch (DeepSeek V4 Flash)
