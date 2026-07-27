# AI CAM Agent Tooling

**Agent orchestration infrastructure for the AI CAM 3.0 autonomous development pipeline.**

This repository defines the agent workflows, skill definitions, pipeline configurations, and coordination protocols used by the AI CAM 3.0 multi-agent system. It does **not** contain CAM engine code — it is the meta-layer that drives autonomous development cycles.

## Purpose

The AI CAM 3.0 pipeline runs a 7-phase autonomous development cycle:

| Phase | Skill | Purpose |
|-------|-------|---------|
| 1 — Research | `cam-research` | Literature mining, competitive analysis, algorithm extraction |
| 2 — Documentation | `cam-documentation` | Module contract writing, API spec generation |
| 3 — Planning | `cam-planning` | Task decomposition, dependency graphing |
| 4 — Development | `cam-development` | TDD implementation, multi-agent parallelism |
| 5 — Testing | `cam-testing` | Test suite orchestration, property-based testing |
| 6 — Auditing | `cam-audit` | Code quality, security, performance, architecture |
| 7 — Criticism | `cam-critic` | Architectural review |

## Structure

```
ai-cam-agent-tooling/
├── locks/              # Distributed file-lock protocol (deadlock prevention)
│   └── LOCK_PROTOCOL.md
├── pipelines/          # Pipeline definitions and orchestration guides
│   ├── full-cycle.json           # 7-phase pipeline config (Hermes cron)
│   └── ORCHESTRATION_GUIDE.md    # Orchestrator execution manual
└── skills/             # Hermes Agent skill definitions for each phase
    ├── SKILL_INDEX.md
    ├── cam-audit/
    ├── cam-critic/
    ├── cam-development/
    ├── cam-documentation/
    ├── cam-orchestration/
    ├── cam-planning/
    ├── cam-post-processor/
    ├── cam-research/
    └── cam-testing/
```

## Tech Stack

- **Hermes Agent** — multi-agent orchestration platform
- **Skills** — YAML+Markdown skill definitions loaded by Hermes profiles
- **Pipeline JSON** — schematic pipeline config (Hermes cron-compatible)

## Status

**Pre-alpha.** Skills are drafted but have not been exercised end-to-end in production. The pipeline is designed for AI CAM 3.0 but lives in a separate repo as reusable orchestration infrastructure.

## Related

- [AI CAM 3.0](https://github.com/Reese000/AI-CAM-3.0) — the CAM engine this tooling orchestrates
