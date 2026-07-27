---
name: cam-testing
description: "Phase 5 — Test suite orchestration: unit, integration, regression, performance, and property-based tests."
version: 0.2.0
author: Hermes Agent
status: active
---

# cam-testing — Concrete Testing Workflow

**Purpose:** Verify implementation quality through multi-layer test suite.

## Test Hierarchy (run in this order, stop on first failure)

### Level 1: Import Chain (must pass before anything else)
```bash
python -c "import kernel, cam, simulation, optimizer, post, api; print('ALL IMPORTS OK')"
python -c "from kernel.types import *; from kernel.sdf import *; from kernel.cam_types import *; print('ALL TYPES IMPORTED')"
```

### Level 2: Unit Tests
```bash
python -m pytest tests/unit/ -v -q -p no:benchmark
```
**Gate:** 100% pass. Any failure blocks the pipeline.

### Level 3: Property-Based Tests
```bash
python -m pytest tests/unit/test_sdf_properties.py -v -q -p no:benchmark
```
**Gate:** 100% pass. Hypothesis should find no counterexamples.

### Level 4: Integration Tests
```bash
python -m pytest tests/integration/ -v -q -p no:benchmark
```
**Gate:** 100% pass across module boundaries.

### Level 5: Regression Tests (Golden Output)
```bash
python -m pytest tests/regression/ -v -q -p no:benchmark
```
Compare against `tests/gcode_output/golden_reference.json`.
**Gate:** Golden output values must match within 5% tolerance.

### Level 6: Performance Benchmarks
```bash
python -m pytest tests/performance/ -v --benchmark-only
```
Compare against previous run benchmarks stored in `.benchmarks/`.
**Gate:** No regression >5% on any benchmark.

### Level 7: Coverage Report
```bash
python -m pytest tests/ --cov=kernel --cov=cam --cov=simulation --cov=post --cov=optimizer --cov-report=term
```
**Gate:** >90% coverage on new code. >50% overall.

## Mutation Testing
```bash
python -m mutmut run --paths-to-mutate kernel/
python -m mutmut results
```
**Gate:** >80% mutation kill rate.
If <80%: add tests that catch the surviving mutations.

## Test Creation Rules for Subagents

Every new function must have:
1. **Normal case test:** typical input → expected output
2. **Edge case test:** 0, empty, None, NaN, extreme values
3. **Error case test:** invalid input → correct exception
4. **(For geometry): Property-based test** with Hypothesis

## Output
- Test results: printed to stdout
- Coverage: `htmlcov/` directory
- Mutation results: `mutmut/` directory

## Toolset
terminal, file

## Profile
cam-test (DeepSeek V4 Flash)
