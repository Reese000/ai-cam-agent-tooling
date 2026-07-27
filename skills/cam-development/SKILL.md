---
name: cam-development
description: "Phase 4 — TDD implementation with multi-agent parallelism and zero technical debt enforcement."
version: 0.2.0
author: Hermes Agent
status: active
---

# cam-development — Concrete Implementation Workflow

**Purpose:** Execute implementation plans via subagent-driven development with mandatory two-stage review.

## Core Workflow

### Step 1: Read Implementation Plan
```
cat docs/plans/[cycle]_[module]_plan.md
```

Extract ALL tasks. Create a todo list showing dependency order.

### Step 2: Dispatch Implementer Subagents (up to 10 concurrent)

Each agent gets ONE bite-sized task (2-5 minutes of work) with:
1. **Task spec** — exact function to implement, with signature
2. **File path** — exact file to write to (must exist or be created)
3. **Test file path** — exact test file to create
4. **TDD cycle** — write test → verify fail → implement → verify pass

**Critical rules for implementers:**
- Write to sandbox tempdir, NOT directly to main tree
- Do NOT read large files (>200 lines) — inline critical code in context
- Do NOT add dependencies not in requirements.txt
- Do NOT write to files outside your task scope
- Must verify tests pass before reporting completion

### Step 3: Spec Compliance Review (MANDATORY — never skip)

After each implementer completes, verify against the task spec:
- [ ] All requirements from spec implemented?
- [ ] Function signatures match spec exactly?
- [ ] File paths match spec?
- [ ] No scope creep (extra methods, features not required)?
- [ ] Behavior matches expected?

**Output:** PASS or specific list of gaps to fix.

### Step 4: Code Quality Review (MANDATORY — never skip)

After spec compliance passes, review:
- [ ] Complete type annotations on all public functions
- [ ] No magic numbers (all constants named and documented)
- [ ] No TODO comments — implement or don't mention it
- [ ] No silent failures (every error path must log or raise)
- [ ] No `except Exception:` — use specific exception types
- [ ] All public functions have docstrings
- [ ] Test covers: normal case, edge case, error case

**Output:** APPROVED or specific list of fixes needed.

### Step 5: Merge & Verify

After both reviews pass:
1. Copy from sandbox to main tree
2. Run: `python -m pytest tests/ -q` — must be 46+ passing
3. Run: `python -c "import kernel, cam, simulation, optimizer, post, api; print('IMPORTS OK')"`
4. If any test fails: revert, fix, re-review, re-merge

## ZERO TECHNICAL DEBT RULES

- No TODO comments — implement it now or delete the feature
- No magic numbers — all constants named with descriptive identifiers
- No silent failures — every error path recorded
- 90%+ test coverage on all new code
- Complete type annotations (mypy strict compatible)

## File Lock Protocol
Before modifying any file in `kernel/`, `cam/`, `simulation/`, `optimizer/`, `post/`, or `api/`:
1. Acquire lock at `agent/locks/[path].lock`
2. Lock hierarchy: types.py > sdf.py > cam/* > simulation/* > optimizer/* > post/* > api/* > tests/*
3. TTL: 300 seconds
4. On conflict: wait 5s (max 3 retries), then escalate to orchestrator

## Toolset
terminal, file, delegation, code_execution

## Profile
cam-dev (DeepSeek V4 Flash)
