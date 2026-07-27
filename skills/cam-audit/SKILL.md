---
name: cam-audit
description: "Phase 6 — Multi-dimensional audit: code quality, security, performance, architecture, and completeness."
version: 0.2.0
author: Hermes Agent
status: active
---

# cam-audit — Concrete Auditing Workflow

**Purpose:** Find what the development phase missed — bugs, security holes, performance issues, design flaws.
**CRITICAL DIFFERENCE from Phase 7 (Criticism):** Audit scans CODE. Criticism reviews ARCHITECTURE. They are not interchangeable.

## Audit Dimensions (run in order)

### 1. Code Quality Audit
```bash
# Static analysis
pylint kernel/ cam/ simulation/ post/ optimizer/ api/ --max-line-length=120 2>&1 | tail -30
mypy kernel/ cam/ simulation/ post/ optimizer/ api/ --strict 2>&1 | tail -30

# Dead code detection
vulture kernel/ cam/ simulation/ 2>&1 | tail -20

# Cyclomatic complexity
python -m pytest --fixtures 2>&1 | head -20
```

**Gate:** pylint >8.0, mypy strict passes, no dead code.

### 2. Security Audit
```bash
# Dependency vulnerability scan
pip-audit 2>&1 | tail -20

# Manual checks on code:
# - Are all file upload paths sanitized?
# - Are all G-code inputs validated before parsing?
# - Is there any authentication bypass?
# - Check: grep -n "eval\|exec\|pickle\|__import__" kernel/ cam/ simulation/ -r
```

**Gate:** Zero critical/high vulnerabilities.

### 3. Performance Audit
```bash
# CPU profiling on core operations
python -m cProfile -s time -m pytest tests/unit/ -q 2>&1 | head -30

# Memory profiling
python -m memory_profiler tests/unit/test_sdf.py 2>&1 | head -20
```

**Gate:** No single function >50% of total runtime.

### 4. Architecture Audit
```bash
# Check for circular imports
python -c "
import kernel, cam, simulation, post, optimizer, api
# If this imports without error, no circular deps at top level
print('No circular imports at module level: OK')
"

# Check for global state singletons
grep -rn "JOBS\|global\|singleton" kernel/ cam/ simulation/ --include="*.py"
```

**Gate:** No circular dependencies, no global state.

### 5. Completeness Audit
- Does every public function have a docstring? `grep -c "def " kernel/sdf.py` vs `grep -c '"""' kernel/sdf.py`
- Does every public function have a test? Compare test file content to source file content.
- Are all error paths tested? Check `except Exception:` blocks match test coverage.
- Are all configuration values validated at startup?

### 6. Edge Case Audit
Look specifically for:
- Empty inputs: `SDF(empty)`, `Mesh(no faces)`, `ToolpathSegment([])`
- Zero values: zero radius, zero feed, zero speed
- Negative values: negative radius (should error), negative coordinates
- Extremes: max coordinate range, max tool diameter vs stock size

## Report Format

Save to `docs/audit/AUDIT_REPORT_[component]_YYYY-MM-DD.md`:

```markdown
# Audit Report: [Component]
Date: YYYY-MM-DD

## Summary
[PASS/FAIL with overall assessment]

## Critical Issues
None | [List with file paths]

## High Issues
- [ ] Issue 1: [description] at [file:line]
  - Fix: [specific code change]

## Medium Issues
- [ ] Issue 1: [description]

## Low Issues
- [ ] Issue 1: [description]

## Approvals
- Code Quality: [PASS/FAIL]
- Security: [PASS/FAIL]
- Performance: [PASS/FAIL]
- Architecture: [PASS/FAIL]
- Completeness: [PASS/FAIL]
```

## Toolset
file, search, terminal

## Profile
cam-audit (Claude Sonnet 4 — best at catching subtle code issues)
