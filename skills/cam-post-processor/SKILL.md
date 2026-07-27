---
name: cam-post-processor
description: "MDP-based post processor generation — AI agent extracts controller specs, generates and verifies post in ~5 minutes."
version: 0.1.0
author: Hermes Agent
status: stub
---

# cam-post-processor (STUB — Fill in during Phase 3)

**Purpose:** Generate verified post processors for any CNC controller from a machine description.

**Workflow:**
1. Receive machine model or controller name
2. Search web for controller programming manual
3. Extract: G-code dialect, cycles, formats, behaviors → fill MDP YAML
4. Generate post.py from MDP template
5. Verify: round-trip through simulation — CL data → post → G-code → re-parse → re-simulate → compare
6. Stress test with 142 edge cases
7. Deliver verified post to library

**Target:** 5 minutes per controller. 50× parallel → 4,000 controllers in ~7 hours.

**Verification gate:**
- Output deviation < 0.001mm at every point
- Feedrates match exactly
- Tool changes match exactly
- Spindle commands match exactly
