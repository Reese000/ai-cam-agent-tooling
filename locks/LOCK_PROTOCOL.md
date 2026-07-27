# AI CAM 3.0 — File-Level Lock Protocol

> **Purpose:** Prevent two subagents from modifying the same file simultaneously.
> **Implemented as:** Distributed lock table at `agent/locks/{file_path}.lock`
> **TTL:** 5 minutes — locks expire automatically if an agent crashes.

## Lock Acquisition

1. Agent wants to modify `kernel/sdf.py`
2. Agent creates `agent/locks/kernel/sdf.py.lock` containing:
   ```json
   {
     "agent_id": "subagent-42",
     "task_id": "task-sdf-octree",
     "acquired_at": "2026-05-10T14:30:00Z",
     "ttl_seconds": 300,
     "files": ["kernel/sdf.py", "kernel/types.py"]
   }
   ```
3. If lock exists: check TTL. If expired (>300s), take lock. 
   If not expired, wait 5s and retry (max 3 retries), then escalate.
4. On lock acquired: proceed with modifications.
5. On modification complete: release lock (delete lock file).

## Lock Hierarchy (Deadlock Prevention)

Locks must be acquired IN ORDER of the lock hierarchy below.
Never acquire lock B while holding lock A if A > B in this hierarchy.

- Layer 1: `kernel/types.py` (most foundational)
- Layer 2: `kernel/sdf.py`
- Layer 3: `cam/*.py` (any)
- Layer 4: `simulation/*.py`
- Layer 5: `optimizer/*.py`, `post/*.py`
- Layer 6: `api/*.py`
- Layer 7: `tests/*.py` (least foundational)

**Rule:** Acquire locks in ascending order. Release in reverse order.

## Read Locks vs Write Locks

- **Read lock** (shared): Any number of agents can hold read lock simultaneously.
- **Write lock** (exclusive): Only one agent can hold, and no read locks can coexist.
- Convention: lock file name `filename.py.lock` = write lock. `filename.py.rlock` = read lock.
